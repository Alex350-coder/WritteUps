# Talent

## Lab Summary

This lab begins with a network scan that identifies an HTTP server running WordPress. From this point, multiple vulnerabilities will be explored, ranging from weak credentials to vulnerable plugins and overly permissive filesystem configurations. The goal is to achieve full compromise, first escalating to a privileged user and ultimately to root. At the end, recommendations are provided to mitigate each attack vector.

---

## Target Identification and Initial Discovery

The lab starts with an `nmap` scan which reveals port 80 open:

![alt text](img/1-nmap.png)

Subsequently, visiting the port reveals a WordPress site:

![alt text](img/2-http.png)

### Available Panels

Various panels are inspected: user registration (disabled), login form, a login error display, and a profile viewer:

![alt text](img/3-registrationpanel.png)

![alt text](img/4logInPanel.png)

![alt text](img/5-ErrorLogInPanel.png)

![alt text](img/6-ProfilePanel.png)

## Initial Authentication and Admin Access

By personal methodology, I tried the user `admin` with a generic basic password, which succeeded. This demonstrates a severe vulnerability in real environments: default or weak credentials allowing administrative access.

With this, access is gained to the WordPress administrator dashboard:

![alt text](img/7-AdminDashBoard.png)

## Exploiting the Vulnerable Plugin

While browsing the dashboard, the **Hello Dolly** plugin is identified; it is known to be highly vulnerable. The Pentest Monkey reverse shell script is used, which is included on most Kali machines:

Typical path: `/usr/share/webshells/php/php-reverse-shell.php`

![alt text](<img/8-PentestMonkeyPhpReverseShell copy.png>)

> **Note:** Remember to specify at the beginning of the script the port where the reverse shell should connect and the attacker machine's IP.

This script must be inserted at the beginning of the plugin's code (to edit it, go to Tools > Plugin Editor) without deleting its usual content, as doing so would remove the plugin. Start a listener on the specified port on the attacker machine using `nc -lnvp PORT` and verify. In this case it succeeds:

![alt text](img/9-ReverseShell.png)

## Stabilizing Access and Internal Enumeration

A **TTY** is spawned on the target to have a more convenient environment:

![alt text](img/10-TYY.png)

The `/etc/passwd` file is read to observe other existing users:

![alt text](img/11-Users.png)

Inspecting `/home` reveals the first flag:

![alt text](img/12-FirstFlag.png)

An interesting file named `backup` is also found in `/opt`:

![alt text](img/13-OptDirectory.png)

![alt text](img/14-catBackup.png)

## Privilege Escalation via sudo

Permissions are checked with `sudo -l`:

![alt text](img/13-Sudo-lWWDATA+.png)

It shows that `python3` can be executed without a password (for some reason the module is not in the Docker environment).

To address this, Python is installed in the container:

1. `sudo docker ps -a` 
2. `sudo docker exec -it <ID_DOCKER> bash -c "apt update && apt install python3 -y"`

Afterward Python is executed as `bobby` to spawn a shell:

```bash
sudo -u bobby /usr/bin/python3 -c 'import os; os.execl("/bin/bash", "bash")'
```

![alt text](img/15-ExecPython3.png)

This opens a shell as that user, and `sudo -l` is checked again:

![alt text](img/16-sudo-L.png)

The result indicates this user can execute the `backup.py` script with sudo without a password; running it triggers recompilation of files. Initially reading `/etc/shadow` was considered but it displayed nothing useful for privilege escalation. The `/opt` directory is inspected, and it is found to be world-writable along with its contents.

![alt text](img/17-RevicionBackupOpt.png)

## Full Compromise: Gaining Root

Therefore the original `backup.py` file is removed and a new one is created with the same name but which simply executes a bash shell as root:

![alt text](img/root.png)

It works, we are root!!!!

## Recommendations

- **Weak/default credentials:** Enforce strong password policies and require changing managed credentials. Use multifactor authentication mechanisms.
- **Vulnerable plugin (Hello Dolly):** Keep plugins/extensions always up to date; remove unnecessary ones and use security whitelists for plugins.
- **Web-accessible plugin editor:** Restrict code editing capabilities from the dashboard, apply validation and sanitization, or disable these functions entirely in production.
- **Broad sudo profiles:** Audit and limit `sudoers` rules, avoiding generic permissions like `NOPASSWD: python3`. Use tools such as `sudoedit` and enforced whitelists.
- **World-writable directories (/opt):** Correct permissions on critical directories, ensuring only administrators have write rights.
