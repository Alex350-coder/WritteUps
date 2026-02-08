# Collections

![alt text](img/Icon.png)

## General Description

**Collections** is a medium-level CTF challenge in Dockerlabs that exposes vulnerabilities related to **WordPress** and vulnerable extensions susceptible to dangerous modifications. Among these is **Hello Dolly**, an extension known for allowing Remote Code Execution (RCE) when activated from the administrative panel.

## Phase 1: Initial Reconnaissance

The reconnaissance process begins in a standard manner with **Nmap** and **Gobuster** scans. To accelerate this procedure, a script located in the scripts repository available in this same space is utilized.

![alt text](img/Nmap.png)
![alt text](img/Gobuster.png)

> **Note:** The script performs, in a single command, a comprehensive port analysis and two Gobuster scans: one using the basic `commons.txt` dictionary and another utilizing the medium-level dictionary from SecList. It is essential to specify the path where these dictionaries are located on the local machine.

Upon analyzing the results, the following open ports are identified:

- **Port 22** — SSH Service
- **Port 80** — HTTP Service
- **Port 27017** — MongoDB Database

## Phase 2: HTTP Service Enumeration

The process proceeds to examine port 80, where a website utilizing a standard WordPress template with generic default information is discovered.

![alt text](img/http.png)

> **Note:** If the page does not load correctly on initial attempt, it is necessary to add the corresponding domain (`collections.dl`) to the `/etc/hosts` file on the line with the IP address `172.17.0.2`.

Upon reviewing the content, it is observed that most of the information is generic without relevant modifications. However, when scrolling down the page, a potential user named **chocolate** is identified:

![alt text](img/httpUser.png)

## Phase 3: WordPress Analysis with WPScan

Given that no obvious additional clues are found, the process proceeds to utilize **WPScan** to obtain additional relevant information:

![alt text](img/WPbegin.png)
![alt text](img/wpAdminPassword.png)

Successfully, WPScan performs an automatic brute-force attack against the `wp-admin.php` panel, which is standard in all WordPress installations. With the obtained credentials, a login session is initiated in the administrative panel:

![alt text](img/httpAdmin.png)
![alt text](img/AdminChocolate.png)

## Phase 4: Exploitation of the Hello Dolly Extension

In the administrative panel, several options can be observed. Among the most relevant are those corresponding to extensions and file uploads. Attempts at file uploads are unsuccessful due to the robustness of the implemented protection. However, a vulnerable extension named **Hello Dolly** is identified, known to be susceptible to RCE through reverse shell payloads, such as the **Pentestmonkey** payload.

![alt text](img/HelloDolly.png)

The process proceeds to locate and insert the payload into the source code of the extension to obtain remote access.

### Payload Utilized:

![alt text](img/PentestMonkey.png)

### Changes Made:

![alt text](img/ChanegesHelloDolly.png)

> **Important Note:** It is essential to modify the IP address and port of the payload with the corresponding values from the attacking machine and the port configured to listen for the reverse shell. The payload is inserted at the beginning of the extension code, preserving the original code of the extension.

## Phase 5: Reverse Shell Acquisition

Following the modification, a web reverse shell is obtained on the attacking machine:

![alt text](img/ReverseShell.png)

Important files are explored using commands such as `ls`, although initially no significant information is found in the current directory.

The process proceeds to read the `/etc/passwd` file and potential users are identified:

- **chocolate**
- **dbadmin**

![alt text](img/catEtcPasswd.png)

## Phase 6: Credentials Extraction

When analyzing the contents of the `/var/www/html/wordpress` directory, several relevant files are found. Particularly important is `wp-config.php`, a file that typically contains critical credentials:

![alt text](img/catConfig-PHP.png)

Credentials related to the database are obtained and, more importantly, the credentials of the **chocolate** user previously observed. To confirm their validity, a brute-force attack is conducted using **Hydra** against port 22 for the chocolate user:

![alt text](img/Hydra.png)

The credentials are successfully confirmed, allowing a login session via SSH:

![alt text](img/sshChocolate.png)

## Phase 7: Privilege Escalation

When listing the files and directories of the chocolate user, a folder related to the MongoDB database is discovered:

![alt text](img/lsChocolate.png)

Upon exploring the content, a history file is found that contains credentials for a user with superior hierarchy, **dbadmin**, previously identified:

![alt text](img/lsMongoChocolate.png)
![alt text](img/catMongoChocolate.png)

Using these credentials, a login session as the **dbadmin** user is successfully achieved via SSH:

![alt text](img/suDbadmin.png)

Having exhausted the escalation options from this user, and considering that the name suggests superior authority, the same password is tested for the **root** user:

![alt text](img/root.png)

**Success!** The password is shared, allowing access as the **root** user and successfully completing the CTF.

## Mitigation Recommendations

### 1. **Credentials Management**
- ✅ Implement unique and complex passwords for each user
- ✅ Use password managers in enterprise environments
- ✅ Avoid predictable administrative user names
- ✅ Apply periodic password changes (every 90 days)
- ✅ Never reuse passwords between users or services

### 2. **WordPress Panel Protection**
- ✅ Change the default `wp-admin.php` URL to a custom one
- ✅ Implement two-factor authentication (2FA)
- ✅ Limit failed login attempts (brute-force protection)
- ✅ Deploy a WAF (Web Application Firewall) to block attacks
- ✅ Disable user enumeration with security plugins

### 3. **Plugins and Extensions Management**
- ✅ Keep all plugins updated constantly
- ✅ Remove unused or outdated plugins
- ✅ Audit custom extension code
- ✅ Restrict plugin code editing to administrator users
- ✅ Implement code review for modifications

### 4. **Configuration Files Security**
- ✅ Protect `wp-config.php` with restrictive permissions (600)
- ✅ Move `wp-config.php` outside the web root directory
- ✅ Avoid exposing configuration files in version control
- ✅ Encrypt sensitive credentials in databases
- ✅ Use environment variables to store credentials

### 5. **Access Control and Privileges**
- ✅ Apply the principle of least privilege
- ✅ Limit SSH access to authorized personnel only
- ✅ Disable direct root login via SSH
- ✅ Use SSH keys instead of passwords
- ✅ Implement sudoers with specific allowed commands

### 6. **Monitoring and Auditing**
- ✅ Implement comprehensive logging of access and changes
- ✅ Monitor failed access attempts (fail2ban)
- ✅ Audit changes to critical system files
- ✅ Review logs regularly for suspicious activities
- ✅ Configure alerts for critical security events

### 7. **System Hardening**
- ✅ Update all services and system components
- ✅ Disable unnecessary services
- ✅ Configure restrictive firewall (only necessary ports)
- ✅ Implement SELinux or AppArmor
- ✅ Perform regular backups with integrity verification

### 8. **Input Validation and Sanitization**
- ✅ Validate all user input in forms
- ✅ Use prepared statements in SQL queries
- ✅ Escape special characters in HTML output
- ✅ Implement Content Security Policy (CSP)
- ✅ Prevent code injection through strict validation

