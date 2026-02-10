
# HackZone

This document concisely describes the walkthrough performed in the "HackZone" CTF, focused on exploiting a file upload to deploy a webshell, discovering exposed credentials, and performing privilege escalation.

## Scenario summary

1. Initial reconnaissance by port scanning and service enumeration.

	- Three relevant ports were identified: `22` (SSH), `53` (DNS) and `80` (HTTP).

	![alt text](Img/nmap.png)

2. DNS enumeration

	- A DNS zone transfer was attempted, yielding useful information for host and name enumeration.

	![alt text](Img/dig.png)

	- Among the results, a potential user named `mrRobot` was discovered.

3. Web enumeration

	- Directory discovery with `gobuster` revealed additional paths once the lab domain (`hackzones.hl`) was specified.

	![alt text](Img/gobuster.png)

	- Resources related to file management were identified: an `uploads` directory and an `upload.php` script responsible for handling uploads.

4. Interaction with the web application

	- The `dashboard.html` page showed a profile section with file upload functionality.

	![alt text](Img/AdminDashboard.png)

	- The upload functionality was abused to upload a PHP webshell.

	![alt text](Img/upload.png)
	![alt text](Img/UploadShellSucces.png)

5. Obtaining a reverse shell

	- Executing the webshell on the server established a reverse connection to the attacker machine (ensure a listener with `nc -lvnp <port>` is running on the attacker machine).

	![alt text](Img/reverseShell.png)

	- The session was upgraded to a proper TTY for interactive use.

	![alt text](Img/tty.png)

6. Credentials and lateral movement

	- Credentials that granted access to the admin panel were found, indicating insecure secret management.

	- Inspecting `/etc/passwd` confirmed the presence of the `mrrobot` user.

	![alt text](Img/usersEtcPasswd.png)

	- A script was found that concatenates hexadecimal values to form a secret string (likely a password).

	![alt text](Img/secretSH.png)
	![alt text](Img/catSecret.png)

	- The derived password allowed switching to `mrrobot` via `su`.

	![alt text](Img/suMrrobot.png)

7. First flag

	- The first flag was located in the `mrrobot` user's directory.

	![alt text](Img/flag1.png)

8. Privilege review and escalation

	- `sudo -l` revealed that the user could run `cat` via `sudo` without a password.

	![alt text](Img/sudo-l.png)

	- Directly reading the second flag was prevented by an access control measure.

	![alt text](Img/think.png)

	- Further inspection revealed a file in `/opt` containing elevated (root) credentials.

	![alt text](Img/RootCredentials.png)

	- Using those credentials, root access was obtained and the second flag was read.

	![alt text](Img/rootSecondFlag.png)


## Vulnerabilities identified

- Unvalidated file uploads allowed execution of server-side code.
- Sensitive credentials exposed in files readable by local users.
- DNS zone transfers were permitted to unauthorized hosts.
- Insecure `sudo` configuration (`NOPASSWD`) enabled potential sensitive file reads.
- Insufficient access control and credential management.

## Mitigations (5 key actions)

1. Harden file upload handling: validate file types and contents, store uploads outside the webroot, and reject executable scripts.
2. Remove credentials from disk and use a secrets manager; rotate credentials regularly.
3. Restrict DNS zone transfers to authorized hosts and consider DNSSEC.
4. Enforce principle of least privilege and remove `NOPASSWD` entries from `sudoers` unless explicitly required and audited.
5. Implement centralized logging, alerts for anomalous activity (file uploads, reverse shells) and network segmentation.


## Conclusion

The lab demonstrates common compromise patterns: insecure uploads, exposed secrets and overly permissive configurations. Remediation requires changes in application logic, secret management and infrastructure configuration.

