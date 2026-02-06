# Security Audit Report: RealGob.DL Laboratory

## Introduction

This report documents the security findings identified during a comprehensive technical evaluation of the Report-Dockerlabs laboratory (RealGob.DL). This laboratory was designed to simulate a real corporate web environment, replicating multiple attack vectors and vulnerabilities common in modern applications. The objective of this audit is to identify, classify, and provide technical recommendations for the mitigation of security risks.

## Methodology

The assessment was conducted following an industry-standard penetration testing methodology:

1. **Reconnaissance**: Identification of active assets through port scanning
2. **Enumeration**: Discovery of accessible services, versions, and directories
3. **Vulnerability Analysis**: Testing for common vulnerabilities (SQLi, XSS, LFI, etc.)
4. **Exploitation**: Confirmation of vulnerabilities through practical testing
5. **Privilege Escalation**: Identification of paths to privileged access

---

## Technical Findings

### 1. Initial Port Scanning

As the starting point of the evaluation, a Nmap port scan was performed that revealed the following active services:

- **Port 80**: HTTP web server (vulnerable web application)
- **Port 22**: SSH service
- **Port 3306**: MySQL database

⚠️ **Important Note**: The `-T4` flag was used in the Nmap scan. This parameter significantly accelerates the scanning process, but in real production environments it should be avoided, as it can generate noise detectable by monitoring systems (IDS/IPS).

![alt text](img/nmap.png)

### 2. HTTP Services Enumeration and Host Configuration

Subsequently, port 80 was analyzed in search of vulnerable content, LFI entry points, and forms susceptible to SQL injection.

**Critical Note**: By default on Linux systems, the redirection of the IP address to the domain `realgob.dl` requires manual entry in the `/etc/hosts` file. Without this configuration, domain resolution will fail.

Multiple functional sections were identified in the web application:
- User authentication form (login)
- New user registration system
- News section
- Contact form
- Informational page about the fictional organization

![alt text](img/inicio.png)

### 3. Hidden Directory Discovery - [HIGH]

A directory scanning was performed using Gobuster, which revealed multiple non-indexed routes on the web server:

![alt text](img/gobuster.png)

**Discovered Directories and Files:**

#### **important.txt** - [MEDIUM]
Text file containing a message from the laboratory creator. Its public exposure allows gathering system information.

![alt text](img/ImportantTXT.png)

#### **info.php** - [CRITICAL]
Exposed sensitive PHP configuration information, including:
- PHP version and loaded modules
- Critical configuration directives
- File system paths
- Enabled extensions

**Impact**: This information facilitates identification of version-specific vulnerabilities and attack vectors.

![alt text](img/infoPHP.png)

#### **Uploads** - [HIGH]
Publicly accessible directory containing documents uploaded by both administrators and users. Public visibility of uploaded files may expose sensitive information.

![alt text](img/Uploads.png)

#### **admin.php** - [HIGH]
Administration panel protected by basic authentication (username and password). However, no additional security mechanisms are implemented such as captchas, rate limiting, or brute force protection.

![alt text](<img/Inicio de secion Admin.png>)

### 4. File Upload Vulnerability - [CRITICAL]

#### "Cargas" Section - File Type Validation Bypass

A critical attack vector was identified in the file upload functionality:

**Attack Flow:**

1. **First Attempt**: Direct attempt to upload a PHP script with `.php` extension was rejected.

![alt text](img/cargasPHP.png)

2. **Initial Rejection**: The server rejected the upload indicating that the file type is not allowed.

![alt text](img/CargasNoPermitido.png)

3. **Validation Bypass**: Using Burp Suite as an intermediary, the POST request was intercepted and the `Content-Type` HTTP header was modified from `application/x-php` to `image/gif`. The server accepted the upload under this false header.

![alt text](img/RepetidorBurp.png)

4. **Successful Upload**: With the modified MIME type, the server allowed the malicious file to be uploaded.

![alt text](img/RepetidoExito.png)

5. **Access to Uploaded File**: The file was stored in the uploads directory and was directly accessible through the browser.

![alt text](img/ArchivoCargado.png)

6. **Code Execution**: Reverse shell access was achieved, allowing execution of system commands with web server privileges.

![alt text](img/ArchivoEjecutadoReverseShell.png)

**Impact**: This vulnerability allows arbitrary code execution on the server with web server user privileges.

### 5. Database Structure Exposure - [HIGH]

#### "database.php" Section - Schema Enumeration

A panel was identified that exposed information about MySQL server databases:

![alt text](img/DatabasePHP.png)

The functional `GOB_BD` database allows viewing:
- Table names
- Column structure
- Data types
- Constraints

![alt text](img/EstruturaDB.png)

**Impact**: Although access credentials are not available at this point, exposure of the database structure provides critical information for designing targeted SQL injection attacks.

### 6. API Enumeration - [MEDIUM]

#### "api.php" Section

A panel was discovered that lists files and elements related to an API used by the application:

![alt text](img/apiPHP.png)

**Potential Impact**: Exposes integration points and possible additional attack vectors.

### 7. Local File Inclusion (LFI) Vulnerability - [CRITICAL]

#### "About" Section with URL Parameter

In the "About" section, a "Read More" button was identified that generates a request with a URL parameter. The parameter was named literally without hash or obfuscation.

![alt text](img/acercaDePHP.png)

**Vulnerable URL**: `http://realgob.dl/about.php?file=iniciativas.html`

![alt text](img/LFIURL.png)

**Proof of Concept**: An attempt was made to load the `/etc/passwd` file through the parameter, and the server returned the file content without validation:

![alt text](img/LFI-etcPasswd.png)

**Impact**: This vulnerability allows arbitrary reading of system files, potentially exposing:
- Sensitive configuration files
- Private keys
- Stored credentials
- Source code

### 8. System Log Exposure - [MEDIUM]

#### "Logs" Section

A panel was identified that exposes two system log files:

![alt text](img/LogsPHP.png)

**Accessible Logs:**

1. **access.log** - HTTP access log

![alt text](img/accesLogs.png)

2. **error.log** - Application error log

![alt text](img/errorLogs.png)

**Note**: In this laboratory, logs present simulated content. However, in a production environment, exposure of real logs would allow **Log Poisoning** attacks, where an attacker injects malicious code into logs that is subsequently executed by monitoring tools.

**Impact**: Exposure of operational information and potential surface for advanced exploitation techniques.

### 9. Cross-Site Scripting (XSS) Vulnerability - [HIGH]

#### Contact Form

A reflected XSS vulnerability was identified in the application's contact form:

![alt text](img/CntactoFromulario.png)

**Successful Payloads**:

- Name field: `<script>alert('XSS')</script>`
- Message field: `<script>alert(1)</script>`

![alt text](img/XSS.png)

![alt text](img/XSSMensaje.png)

**Impact**: An attacker can inject malicious JavaScript code that will execute in the browser of users viewing the form, allowing:
- Session theft (cookies)
- Redirection to malicious sites
- Credential capture

### 10. Authentication and Authorization Vulnerabilities - [CRITICAL]

#### Login and Registration Forms

Multiple critical vulnerabilities were identified in the authentication module:

![alt text](img/IniciasSecionPHP.png)

**Vulnerability A: User Enumeration - [HIGH]**

The login and registration forms generate differentiated error messages:

- Incorrect user error: "User not found"
- Incorrect password error: "Incorrect Passsword"

![alt text](img/NombreIncorrecto.png)

![alt text](img/formularioDeSecionContraseñaIncorrecta.png)

This behavior allows enumeration attacks to identify valid users in the system, facilitating subsequent brute force attempts against existing users.

**Vulnerability B: Insecure Direct Object Reference (IDOR) - [CRITICAL]**

After logging in, a user panel is accessed that contains personal information:

![alt text](img/TestUserCreated.png)

The panel URL contains a sequential numeric ID parameter: `?id=2`

By modifying the ID parameter value, an attacker can access information of other system users without authorization:

![alt text](img/CuentaDeUsuarioPorID.png)

**Impact**: Unauthorized access to private information of other users, including:
- Personal data
- Contact information
- Associated financial information

### 11. Financial Transaction Manipulation Vulnerability - [CRITICAL]

#### Fund Transfer Functionality

A critical vulnerability was identified in the money transfer module between accounts:

**Test A: Transfer with Negative Amount**

An attempt was made to initiate a transfer with a negative amount (-200):

![alt text](img/TestTRansferencia.png)

The system accepted the negative transfer. The result was:
- Funds added to the current user's account
- Funds subtracted from the destination account

![alt text](img/TransferenciaCorrecta.png)

**Impact on Accounts:**

Account that sent money (should subtract): Funds were added

![alt text](img/CuentConDineroTransferido.png)

Receiving account: Funds were subtracted, ending with negative balance

![alt text](img/CuentaTestDespuesTransferencia.png)

**Identified Vulnerabilities:**

1. **Lack of Amount Validation**: No validation that amount is positive
2. **No Balance Verification**: No check if source account has sufficient funds
3. **Inverted Logic**: Amounts are applied in reverse order
4. **Money Creation**: System allows creating money from nothing through negative transfers

**Impact**: An attacker can infinitely increase their balance, altering the integrity of the financial system.

### 12. Exposed Git Repository Discovery - [CRITICAL]

#### "desarrollo.php" Section and .git Folder

An additional administration panel was identified under the `/desarrollo` route:

![alt text](img/DesarrolloPHP.png)

A directory scan specific to `/desarrollo` revealed the existence of a Git repository:

![alt text](img/GobusterDesarrollo.png)

**Exploitation with git-dumper:**

The `git-dumper` tool was used to fully extract the published Git repository:

![alt text](img/gitDumperClone.png)

```bash
python3 -m venv venv
source venv/bin/activate
pip3 install -r requirements.txt
python3 git_dumper.py http://realgob.dl/desarrollo/.git/ dump
```

**Extracted Content:**

The repository files contained critical information:

![alt text](img/CatInfoGitDump.png)

Extracted items include:
- Potential usernames for SSH
- Associated passwords or keywords

**Impact**: Exposure of source code, change history, and credentials embedded in configuration files.

### 13. SSH Brute Force Access - [CRITICAL]

Using information extracted from the Git repository, a list of users and passwords was compiled to execute a brute force attack against the SSH service (port 22):

![alt text](img/hydraSSH.png)

The attack was successful, obtaining valid SSH access credentials:

![alt text](img/sshADM.png)

### 14. Privilege Escalation via Configuration Files - [CRITICAL]

After obtaining SSH access as a low-level user, a search was conducted for configuration files that commonly contain plain text credentials:

![alt text](img/ConfigDocumentsADM.png)

3 configuration files were discovered. The last two contained identical and critical information:
- MySQL database credentials
- Internal service access information

![alt text](img/DBInfo.png)

![alt text](img/DBinfo2.png)

These files provided direct access to the MySQL database on port 3306 (which would otherwise be protected).

### 15. Credential Extraction from Environment Variables - [CRITICAL]

System environment variables were reviewed:

![alt text](img/rootPasswd.png)

A suspicious environment variable was identified: `MY_PASS` containing a hexadecimal format value.

An online conversion tool was used to decode the hexadecimal value to plain text:

![alt text](img/DecofdificacionPasswdRoot.png)

The root password was obtained and successfully validated:

![alt text](img/root.png)

**Impact**: Root level access, total system compromise.

---

## Conclusion

The Report laboratory exposes a critical and varied set of vulnerabilities that, together, allow complete system compromise from anonymous access to code execution with root privileges.

**Key Findings:**

1. **Multiple Critical Vulnerabilities**: The system contains a minimum of 6 vulnerabilities classified as CRITICAL
2. **Sensitive Information Exposure**: The application exposes configuration information, database structure, and system files
3. **Linear Attack Chain**: Vulnerabilities are interconnected, allowing progressive privilege escalation from anonymous access
4. **Insufficient Validation Control**: Lack of validation in multiple layers (file types, numeric values, URL parameters)
5. **Poor Credential Management**: Credentials are exposed in multiple locations without encryption

**Overall Risk**: CRITICAL

The application should not be deployed in a production environment without comprehensive remediation of identified vulnerabilities.

---

## Recommendations and Solutions

### Critical Vulnerabilities

#### [CRITICAL] 1. info.php and PHP Configuration Exposure

**Problem**: The `info.php()` file exposes complete PHP configuration.

**Solution**:
```bash
# Remove or rename the file
rm /var/www/html/info.php

# Alternative: Protect with .htaccess authentication
cat > /var/www/html/info.php.htaccess << 'EOF'
<Files info.php>
    Order Allow,Deny
    Deny from all
</Files>
EOF
```

---

#### [CRITICAL] 2. File Upload Vulnerability - MIME Validation Bypass

**Problem**: File type validation is performed only via `Content-Type`, which can be modified by the client.

**Solution**:

```php
<?php
$uploadDir = '/uploads/';
$maxFileSize = 5 * 1024 * 1024;

$allowedExtensions = ['jpg', 'jpeg', 'png', 'gif', 'pdf'];
$allowedMimeTypes = ['image/jpeg', 'image/png', 'image/gif', 'application/pdf'];

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $file = $_FILES['upload'];
    
    if ($file['size'] > $maxFileSize) {
        die('File too large');
    }
    
    $ext = strtolower(pathinfo($file['name'], PATHINFO_EXTENSION));
    if (!in_array($ext, $allowedExtensions)) {
        die('File type not allowed');
    }
    
    $finfo = new finfo(FILEINFO_MIME_TYPE);
    $mime = $finfo->file($file['tmp_name']);
    
    if (!in_array($mime, $allowedMimeTypes)) {
        die('MIME type not allowed');
    }
    
    $newFileName = bin2hex(random_bytes(16)) . '.' . $ext;
    if (move_uploaded_file($file['tmp_name'], $uploadDir . $newFileName)) {
        echo 'File uploaded successfully';
    }
}
?>
```

---

#### [CRITICAL] 3. Local File Inclusion (LFI)

**Problem**: The `page` parameter is processed directly without validation.

**Solution**:

```php
<?php
$allowedPages = ['principal', 'historia', 'mision', 'vision', 'equipo'];
$page = isset($_GET['page']) ? basename($_GET['page']) : 'principal';

if (!in_array($page, $allowedPages)) {
    $page = 'principal';
}

$filePath = __DIR__ . '/pages/' . $page . '.php';
if (file_exists($filePath)) {
    include $filePath;
}
?>
```

---

#### [CRITICAL] 4. IDOR in User Information

**Problem**: User IDs are sequential without authorization verification.

**Solution**:

```php
<?php
session_start();

$requestedUserId = isset($_GET['id']) ? intval($_GET['id']) : null;

if ($requestedUserId !== $_SESSION['user_id']) {
    http_response_code(403);
    die('Permission denied');
}

$stmt = $pdo->prepare('SELECT id, nombre, email FROM usuarios WHERE id = ?');
$stmt->execute([$_SESSION['user_id']]);
$user = $stmt->fetch();
?>
```

---

#### [CRITICAL] 5. Financial Transaction Manipulation

**Problem**: Lack of amount validation and balance verification.

**Solution**:

```php
<?php
$monto = floatval($_POST['monto']);

if ($monto <= 0) {
    die('Amount must be greater than zero');
}

$stmt = $pdo->prepare('SELECT saldo FROM usuarios WHERE id = ?');
$stmt->execute([$origen]);
$userData = $stmt->fetch();

if ($userData['saldo'] < $monto) {
    die('Insufficient balance');
}

$pdo->beginTransaction();
$pdo->prepare('UPDATE usuarios SET saldo = saldo - ? WHERE id = ?')->execute([$monto, $origen]);
$pdo->prepare('UPDATE usuarios SET saldo = saldo + ? WHERE id = ?')->execute([$monto, $destino]);
$pdo->commit();
?>
```

---

#### [CRITICAL] 6. Exposed Git Repository

**Problem**: The `.git` directory is publicly accessible.

**Solution - nginx**:
```nginx
location ~ /\.git {
    deny all;
}
```

**Solution - Apache**:
```apache
<FilesMatch "^\.">
    Deny from all
</FilesMatch>
```

---

### High Vulnerabilities

#### [HIGH] 7. User Enumeration

**Problem**: Differentiated error messages allow user enumeration.

**Solution**:

```php
<?php
$stmt = $pdo->prepare('SELECT id, contraseña FROM usuarios WHERE usuario = ?');
$stmt->execute([$usuario]);
$user = $stmt->fetch();

// Generic message for both cases
if (!$user || !password_verify($contraseña, $user['contraseña'])) {
    die('Incorrect username or password');
}
?>
```

---

#### [HIGH] 8. XSS in Contact Form

**Problem**: No input validation or sanitization.

**Solution**:

```php
<?php
$nombre = htmlspecialchars($_POST['nombre'], ENT_QUOTES, 'UTF-8');
$mensaje = htmlspecialchars($_POST['mensaje'], ENT_QUOTES, 'UTF-8');
$email = filter_var($_POST['email'], FILTER_SANITIZE_EMAIL);

$stmt = $pdo->prepare('INSERT INTO contactos VALUES (?, ?, ?, NOW())');
$stmt->execute([$nombre, $email, $mensaje]);
?>
```

---

#### [HIGH] 9. Unauthorized SSH Access

**Problem**: Weak passwords or exposed in Git files.

**Solution**:

```bash
passwd adm
passwd root

sudo nano /etc/ssh/sshd_config
# PasswordAuthentication no
# PermitRootLogin no

sudo systemctl restart ssh
```

---

### Medium Vulnerabilities

#### [MEDIUM] 10. Configuration File Exposure

**Problem**: Config files contain plain text credentials.

**Solution**: Use environment variables instead of hardcoded files.

```bash
export DB_HOST="localhost"
export DB_USER="app_user"
export DB_PASS="strong_generated_password"
```

---

#### [MEDIUM] 11. Log Exposure

**Problem**: Logs are publicly accessible.

**Solution - nginx**:
```nginx
location ~^/logs/ {
    deny all;
}
```

---

## Remediation Schedule

| Priority | Finding | Timeline |
|----------|---------|----------|
| 1 | .git blocking | Immediate |
| 2 | info.php | 24 hours |
| 3 | File upload validation | 3 days |
| 4 | LFI protection | 3 days |
| 5 | IDOR in profiles | 5 days |
| 6 | Transaction validation | 7 days |
| 7 | XSS in forms | 5 days |
| 8 | Secure SSH | 3 days |

---

**Executive Summary**: The Report laboratory presents critical vulnerabilities that completely compromise system security. Immediate remediation of all CRITICAL findings is required before any production deployment.
