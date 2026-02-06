# Introduction to Password Cracking

Passwords are commonly hashed when stored.

Hashing is a mathematical function which transforms an arbitrary number of input bytes into a fixed-size output (e.g., MD5).

Hash functions are one-way; attempting to reverse them is password cracking.

Many techniques: rainbow tables, dictionary attacks, and brute-force attacks.

- Rainbow tables are large pre-compiled maps of input and output values for a given hash function. These can be used to very quickly identify the password if its corresponding hash has already been mapped.

Using a salt before a password is hashed helps avoid rainbow tables. A salt, in cryptographic terms, is a random sequence of bytes added to a password before it is hashed.

- A brute-force attack involves attempting every possible combination of letters, numbers, and symbols until the correct password is discovered.

- A dictionary attack, otherwise known as a wordlist attack, is one of the most efficient techniques for cracking passwords, especially when operating under time constraints as penetration testers usually do. Rather than attempting every possible combination of characters, a list containing statistically likely passwords is used. Well-known wordlists for password cracking are rockyou.txt and those included in SecLists.

Example of a command to obtain the hash of a string:

```bash
echo -n 'Academy#2025' | sha1sum
```
