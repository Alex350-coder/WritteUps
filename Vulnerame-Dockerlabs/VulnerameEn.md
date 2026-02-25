# Vulnerame - Exploitation Lab

## Executive summary

This lab guides the student through identifying and exploiting several vulnerabilities on a target machine running common services (SSH, HTTP, and MySQL) and a CMS (Joomla). The goal is to demonstrate practical techniques: reconnaissance, exploiting a known CMS vulnerability to obtain credentials, privilege escalation using cracked passwords, obtaining a reverse shell, and a final escalation to root by exploiting a writable privileged binary.

## Lab walkthrough

The exercise begins with a port and service scan to identify the attack surface. The discovered ports and services include SSH (22), HTTP (80), and MySQL (3306):

![alt text](img/1-nmap.png)

A directory scan with Gobuster suggests a WordPress installation:

![alt text](img/2-gopbuster.png)

Visiting the URL in a browser shows a Joomla CMS template rather than WordPress as suggested by the directory scan:

![alt text](img/3-http.png)

After initial searching, a Joomla 4 version is identified and a recent CVE is found that allows extraction of database credentials (username and password) and listing privileged users (username and email):

![alt text](img/5-CVE.png)

The exploit is used to extract credentials from the vulnerable Joomla installation:

![alt text](img/6-UsingCVE.png)
![alt text](img/7-UsingCVE.png)

Among the recovered credentials there is an active database user with its password, allowing access to MySQL. Inside the database we find `joomla_db` and the `users` table, which contains a username and a hashed password for a site user (the username had been discovered previously via the CVE, but without a password):

![alt text](img/8-Mysql.png)

The stored password is hashed, so John the Ripper is used to crack the hash and recover the plaintext password:

![alt text](img/9-DecrypthPassword.png)

With the recovered password, an administrator login to Joomla is performed:

![alt text](img/10-AdminDashboard.png)

After testing several attack vectors without success, a reverse shell is attempted. The strategy consists of modifying editable web content (for example `index.html`) and inserting a PentestMonkey script that triggers a reverse shell to the attacker machine.

> Note: prepare a listener on the attacker machine with `nc -lnvp PORT` before executing the payload.

![alt text](img/11-PentestMonkey.png)

Subsequently, a reverse shell is received on the attacker machine (port 4444 in this case):

![alt text](img/12-ReverseShell.png)

From the reverse shell the `/etc/passwd` file is inspected to find local users useful for privilege escalation:

![alt text](img/13-CatETCpasswd.png)

Two SSH-capable users are identified: `guadalupe` and `ignacio`. A brute-force attack against SSH is attempted and succeeds, providing access to one account:

![alt text](img/14-Hydra.png)

SSH login as `guadalupe` yields no clear escalation path after reviewing the environment:

![alt text](img/15-sshGuadalupe.png)

SSH login as `ignacio` shows that the user can run Ruby via a specific privileged system binary configured through `sudo`:

![alt text](img/16-sshSudo-lIgnacio.png)

The binary is analyzed and tested to determine its behavior:

![alt text](img/17-Pruebas.png)

It is discovered that the binary is writable by the user, which allows inserting `system "/bin/sh"` so that when executed it spawns a shell with elevated privileges. Running the modified binary yields a root shell:

![alt text](img/18-root.png)

Finally, the `root` account is obtained.

## Mitigation recommendations

1. Vulnerability: Exposed Joomla CMS with a CVE allowing credential extraction.
   - Recommendation: Keep Joomla and all components patched; apply security updates promptly and restrict administrative access from public networks.

2. Vulnerability: Extraction of database credentials from the CMS installation.
   - Recommendation: Avoid storing sensitive credentials in application-accessible locations; use environment variables with restricted permissions and secret managers to store database credentials.

3. Vulnerability: Weak passwords or inadequate hashing enabling hash cracking.
   - Recommendation: Use strong password hashing algorithms (bcrypt, Argon2) with per-user salts and enforce strong password policies (minimum length, complexity, and account lockout after failed attempts).

4. Vulnerability: Editable web content allowing insertion of malicious payloads (reverse shell).
   - Recommendation: Limit edit capabilities to minimal roles, validate and sanitize all editable content, deploy a WAF to filter suspicious payloads, and audit content changes with version control.

5. Vulnerability: SSH brute-force susceptibility due to lack of protections.
   - Recommendation: Enforce key-based SSH authentication (disable password auth), implement rate limiting and intrusion prevention (fail2ban), and consider multi-factor authentication for sensitive accounts.

6. Vulnerability: Writable privileged binary or misconfigured `sudo` allowing elevated command execution.
   - Recommendation: Harden `sudoers` policies, remove unnecessary privileges, ensure privileged binaries are immutable to unprivileged users, and use file integrity monitoring (AIDE, Tripwire).

---

**Note**: All actions must be performed only in controlled environments with explicit authorization. Using these techniques on unauthorized systems is illegal.
