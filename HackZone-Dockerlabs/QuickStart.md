# QuickStart — Resumen rápido (ES / EN)
## ES — Instrucciones básicas para completar el laboratorio en minutos
1) Escaneo y enumeración básica:
```bash
# Escanear puertos y servicios (reemplazar <TARGET> por la IP/host objetivo)
nmap -sC -sV -p- --open -oN nmap.txt <TARGET>

# Probar transferencia de zona DNS (si aplica)
dig axfr @<TARGET> <domain>

# Búsqueda de directorios
gobuster dir -u http://<TARGET> -w /usr/share/wordlists/dirb/common.txt
```
2) Buscar y evaluar funcionalidad de subida de archivos

- Identificar `upload.php` o directorios `uploads` y revisar restricciones.
- Si la validación es insuficiente, preparar una webshell PHP segura para pruebas.
3) Obtener shell reversa

```bash
# En la máquina atacante:
nc -lvnp 4444

# Subir y ejecutar la webshell desde la aplicación web; luego interactuar con la sesión.
```
4) Escalar y recuperar banderas

- Revisar `/etc/passwd`, home directories y archivos expuestos.
- Buscar scripts que generen credenciales o contraseñas (hexadecimales, concatenaciones).
- Usar `su` o credenciales encontradas para cambiar de usuario y buscar `flag1`.
- Ejecutar `sudo -l` para identificar comandos con privilegios y buscar vectores de escalada.
## EN — Quick steps to finish the lab in minutes

1) Basic discovery:

```bash
# Scan all ports and enumerate services (replace <TARGET>)
nmap -sC -sV -p- --open -oN nmap.txt <TARGET>

# Attempt DNS zone transfer
dig axfr @<TARGET> <domain>

# Discover web directories
gobuster dir -u http://<TARGET> -w /usr/share/wordlists/dirb/common.txt
```
2) Identify file upload functionality

- Locate `upload.php` and `uploads` directory; assess validation.
- If allowed, craft a non-destructive PHP webshell for testing.
3) Acquire a reverse shell

```bash
# On the attacker machine:
nc -lvnp 4444

# Trigger the webshell from the target and interact with the session.
```
4) Privilege escalation and flags

- Inspect `/etc/passwd`, user home directories and readable files.
- Look for scripts that assemble secrets (hex strings, concatenation).
- Use discovered credentials to `su` to the user and retrieve `flag1`.
- Run `sudo -l` to find privileged commands and use them to escalate to root and retrieve `flag2`.
## Notas de seguridad / Safety notes

- Realice pruebas únicamente en entornos de laboratorio controlados.
- No deje credenciales de prueba en texto plano en sistemas reales.
QuickStart — Resumen rápido (ES / EN)
ES — Instrucciones básicas para completar el laboratorio en minutos

1) Escaneo y enumeración básica:
```bash
# Escanear puertos y servicios (reemplazar <TARGET> por la IP/host objetivo)
nmap -sC -sV -p- --open -oN nmap.txt <TARGET>

# Probar transferencia de zona DNS (si aplica)
dig axfr @<TARGET> <domain>

# Búsqueda de directorios
gobuster dir -u http://<TARGET> -w /usr/share/wordlists/dirb/common.txt
```

2) Buscar y evaluar funcionalidad de subida de archivos

- Identificar `upload.php` o directorios `uploads` y revisar restricciones.
- Si la validación es insuficiente, preparar una webshell PHP segura para pruebas.

3) Obtener shell reversa

```bash
# En la máquina atacante:
nc -lvnp 4444

# Subir y ejecutar la webshell desde la aplicación web; luego interactuar con la sesión.
```

4) Escalar y recuperar banderas

- Revisar `/etc/passwd`, home directories y archivos expuestos.
- Buscar scripts que generen credenciales o contraseñas (hexadecimales, concatenaciones).
- Usar `su` o credenciales encontradas para cambiar de usuario y buscar `flag1`.
- Ejecutar `sudo -l` para identificar comandos con privilegios y buscar vectores de escalada.

E