# Quick Start — Resolución rápida del laboratorio (ES / EN)

## Español — Pasos críticos (resumen)

1. Escaneo rápido: `nmap -sC -sV -p- TARGET` para identificar puertos (22,80,3306).
2. Enumeración web: `gobuster dir -u http://TARGET -w WORDLIST` para encontrar rutas.
3. Identificar CMS: comprobar página web y versión (Joomla 4). Buscar CVE aplicable.
4. Explotar CVE: usar el exploit para extraer credenciales del CMS (DB y usuarios privilegiados).
5. Acceso MySQL: conectarse con credenciales encontradas y revisar `joomla_db`.`users`.
6. Romper hash: exportar el hash y usar `john --wordlist=rockyou.txt hashfile` para obtener la contraseña.
7. Acceso admin: iniciar sesión en el panel de Joomla con las credenciales descifradas.
8. Reverse shell: modificar contenido editable (p. ej. `index.html`) con el payload de PentestMonkey y preparar `nc -lnvp 4444` en atacante.
9. Obtener shell: recibir la reverse shell y enumerar sistema (`/etc/passwd`, permisos, binarios con SUID/sudo).
10. SSH y escalada: probar SSH (brute force si es necesario); con `ignacio` usar `sudo -l` y revisar binarios editables.
11. Escalada final: si un binario privilegiado es editable, insertar `system "/bin/sh"` y ejecutarlo para obtener `root`.

## English — Critical steps (summary)

1. Quick scan: `nmap -sC -sV -p- TARGET` to discover ports (22,80,3306).
2. Web enumeration: `gobuster dir -u http://TARGET -w WORDLIST` to find directories.
3. Identify CMS: check site and version (Joomla 4). Search for applicable CVE.
4. Exploit CVE: use the exploit to extract CMS credentials (DB and privileged users).
5. MySQL access: connect with recovered credentials and inspect `joomla_db`.`users`.
6. Crack hash: export the hash and use `john --wordlist=rockyou.txt hashfile` to recover the password.
7. Admin login: sign into Joomla admin with cracked credentials.
8. Reverse shell: modify editable content (e.g. `index.html`) with PentestMonkey payload and run `nc -lnvp 4444` on attacker.
9. Get shell: accept the reverse shell and enumerate the host (`/etc/passwd`, permissions, SUID/sudo binaries).
10. SSH & escalation: try SSH (brute force if needed); for `ignacio` run `sudo -l` and inspect writable privileged binaries.
11. Final escalation: if a privileged binary is writable, insert `system "/bin/sh"` and run it to spawn a `root` shell.

---

Use these steps as a checklist to resolve the lab quickly. For full details and context, refer to the main documentation files.

