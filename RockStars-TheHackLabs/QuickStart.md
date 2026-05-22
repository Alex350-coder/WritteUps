# Quick Start / Inicio rápido

## Español

1. Escaneo inicial: usa `nmap` para detectar puertos 22 y 80.
2. Enumeración web: usa `gobuster` para encontrar directorios y revisa `index.php`.
3. Explotación LFI: haz fuzzing del parámetro POST `backdoor`, confirma con `curl` y lee `/etc/passwd`.
4. Recupera credenciales: usa LFI para leer `db.php` y obtener el usuario `shark`.
5. Accede vía SSH a `shark`, revisa `sudo -l` y explota el binario `bof` para obtener shell como `wvverez`.
6. Localiza el ZIP protegido, transfiérelo con `wget` y craquea la contraseña con `john` y `rockyou.txt`.
7. Usa las credenciales extraídas para SSH a `loseey`, inspecciona `rubiales.py`, crea `psutil.py` malicioso y obtén shell como `username3`.
8. Revisa `sudo -l` en `username3`, encuentra `bsh` como root y explota el entorno para escalar a root.
9. Busca la flag final en el directorio root.

## English

1. Initial scan: use `nmap` to detect ports 22 and 80.
2. Web enumeration: run `gobuster` to find directories and inspect `index.php`.
3. LFI exploitation: fuzz the POST parameter `backdoor`, confirm with `curl`, and read `/etc/passwd`.
4. Retrieve credentials: use LFI to read `db.php` and obtain the `shark` user.
5. SSH to `shark`, check `sudo -l`, and exploit the `bof` binary to get a shell as `wvverez`.
6. Locate the password-protected ZIP, transfer it with `wget`, and crack the password with `john` and `rockyou.txt`.
7. Use the extracted credentials to SSH as `loseey`, inspect `rubiales.py`, create a malicious `psutil.py`, and gain a shell as `username3`.
8. Check `sudo -l` as `username3`, identify `bsh` as root, and exploit the environment to escalate to root.
9. Find the final flag in the root directory.
