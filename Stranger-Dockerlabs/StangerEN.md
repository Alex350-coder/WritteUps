# Stranger

## Lab summary

This lab walks the user through reconnaissance, enumeration and privilege escalation on a vulnerable machine. The workflow demonstrates service discovery, web content enumeration, credential retrieval, and escalating privileges to root.

## Service discovery

An initial `nmap` scan reveals three open ports: 21 (FTP), 22 (SSH) and 80 (HTTP).

![alt text](img/nmap.png)

## Web enumeration

The web root contains a subtle hint: "Welcome mwheeler". A directory enumeration with `gobuster` discovers a resource named `strange`.

![alt text](img/gobuster.png)

Inside `strange` the page contains thematic content and a clear clue: "The password for the encrypted file is iloveyou".

![alt text](img/httpStranger.png)

Further enumeration reveals two important files: `private.txt` (RSA-encrypted) and `secret.html`, which hints at an FTP user `admin` whose password must be obtained.

![alt text](img/httpSecret.png)

## FTP brute-force attack

Using the clue from `secret.html`, `hydra` is used to brute-force the FTP account `admin`. The attack succeeds and yields access to a file that appears to be a private key.

![alt text](img/hydraAdmin.png)

The private key is retrieved via FTP (`get`) and then used to decrypt `private.txt`, revealing a string.

![alt text](img/ftpAdmin.png)

![alt text](img/demorgogon.png)

## Initial SSH access and pivot

The decrypted word is tested as a credential on SSH and grants access to `mwheeler`.

![alt text](img/sshMwheler.png)

From `mwheeler` the `su` command is used with the `admin` password to switch to the `admin` account.

![alt text](img/suAdmin.png)

## Privilege escalation to root

As `admin`, `sudo -l` reveals the ability to run privileged commands. This capability is leveraged to obtain a root shell.

![alt text](img/root.png)

Accessing `/root` reveals the final flag and completes the exercise.

![alt text](img/flaggroot.png)

## Mitigation recommendations

- Minimize exposed services: disable legacy FTP or replace it with SFTP/FTPS.
- Enforce strong password policies and account lockout to mitigate brute-force attacks.
- Monitor authentication logs and deploy IDS/IPS to detect enumeration and brute-force attempts.
- Protect private keys with strict file permissions and secure storage; never leave keys accessible over public services.
- Audit `sudo` privileges and apply least-privilege principles.
- Keep all services patched and up to date.
- Avoid placing sensitive hints or credentials in publicly accessible web content.

