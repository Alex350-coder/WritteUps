## Quick Start / Inicio rápido

Español:

- 1) Escaneo rápido: `nmap -sC -sV -p- <IP>`.
- 2) Enumeración web: `gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt`.
- 3) Revisar páginas encontradas; buscar formularios y contenido oculto (Base64).
- 4) Probar inyección SQL en el panel de admin (usuario: `notadmin@gmail.com`).
- 5) Si hay procesamiento XML, probar XXE para leer `/etc/passwd` y descubrir usuarios.
- 6) Intentar SSH usando usuario como contraseña (pista: "la contraseña es tu usuario").
- 7) Decompilar archivos `.pyc` encontrados para recuperar credenciales.
- 8) Revisar `sudo` y utilidades instaladas (`croc`) para escalada a root.

English:

- 1) Quick scan: `nmap -sC -sV -p- <IP>`.
- 2) Web enumeration: `gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt`.
- 3) Inspect discovered pages; search for forms and hidden content (Base64).
- 4) Attempt SQL injection on the admin panel (user: `notadmin@gmail.com`).
- 5) If XML processing exists, test for XXE to read `/etc/passwd` and enumerate users.
- 6) Try SSH auth using the username as password (hint: "the password is your username").
- 7) Decompile any found `.pyc` files to recover credentials.
- 8) Check `sudo` permissions and file-transfer tools (`croc`) for root escalation.

Notas: sustituir `<IP>` por la dirección objetivo. Use Burp Suite para pruebas SQLi/XXE más cómodas.
