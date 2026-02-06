# Attaquer le Gestionnaire d’identifiants Windows

Credential Manager (Windows Vault / Credential Locker) permet de stocker des identifiants chiffrés pour utilisateurs et applications. Les magasins chiffrés se trouvent typiquement ici :
- %UserProfile%\AppData\Local\Microsoft\Vault\
- %UserProfile%\AppData\Local\Microsoft\Credentials\
- %UserProfile%\AppData\Roaming\Microsoft\Vault\
- %ProgramData%\Microsoft\Vault\
- %SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\

Chaque dossier de vault contient un fichier Policy.vpol avec des clés AES (AES-128/256) protégées par DPAPI. Sur les versions récentes, Credential Guard protège davantage les clés DPAPI via des enclaves mémoire (Virtualization-based Security).

Terminologie
- Credential Manager : API/interface utilisateur.
- Credential Locker / Vault : magasins chiffrés sous-jacents.

Types d’identifiants
- Web Credentials : pour sites/ comptes en ligne (IE / Edge legacy).
- Windows Credentials : tokens de services, ressources réseau, utilisateurs de domaine, etc.

Export / sauvegarde
- Les vaults peuvent être exportés en .crd (via le Panneau de configuration ou commande) ; les sauvegardes sont chiffrées avec un mot de passe utilisateur.
- Exemple d’ouverture de l’interface d’identifiants :
    ```powershell
    rundll32 keymgr.dll,KRShowKeyMgr
    ```

Énumération avec cmdkey
- Lister les identifiants du profil courant :
    ```powershell
    cmdkey /list
    ```
- Format courant de sortie :
    - Target : ressource/compte
    - Type : Generic, Domain Password, etc.
    - User : compte associé
    - Persistence : persistance locale/système

Impersonation via runas
- Si un identifiant de type Domain:interactive est présent, on peut utiliser runas pour lancer un processus en tant que cet utilisateur (si les crédentials sont accessibles) :
    ```powershell
    runas /savecred /user:DOMAINE\utilisateur cmd
    ```

Extraction / déchiffrement
- Outils courants pour récupérer/déchiffrer les identifiants :
    - mimikatz (sekurlsa, dpapi), SharpDPAPI, LaZagne, DonPAPI
- Exemple minimal d’utilisation de mimikatz :
    ```text
    mimikatz.exe
    privilege::debug
    sekurlsa::credman
    ```
    (sekurlsa permet d’extraire les données en mémoire ; dpapi permet de traiter des blobs DPAPI/Policy.vpol)

Remarques
- Les clefs AES dans Policy.vpol sont protégées par DPAPI ; sans accès aux secrets DPAPI (ou sans contourner Credential Guard), il est difficile de déchiffrer les vaults.
- Respecter toujours le cadre légal et les politiques en vigueur lors d’audits ou tests de sécurité.
- Outils supplémentaires : SharpDPAPI, LaZagne, DonPAPI.
