# Quick Start / Inicio Rápido

## Español
1. Escanee la máquina con `nmap` para identificar puertos (80, 1789, 3690).
2. Use `gobuster` en el puerto 80; descargue el archivo en `/upload` y lea el
   consejo sobre Subversion.
3. Acceda a `http://<IP>/subversion`; observe el requerimiento de credenciales.
4. Ejecute el script Python de fuerza bruta contra `svnuser` para obtener la
   contraseña.
5. Ingrese al repositorio SVN y descargue el binario vulnerable.
6. Analice el ejecutable con Ghidra, identifique buffer overflow y la función
   `shell`.
7. Cree un exploit Python que responda preguntas, calcule el número pseudo‑aleatorio
   y desborde el buffer para ejecutar `shell`.
8. Obtenga shell como usuario `luigi`.
9. Verifique procesos y revise `/etc/crontab`; identifique la tarea `tar` en
   `/home/luigi/*`.
10. Cree archivos `--checkpoint*` y un script que ponga SUID a `/bin/bash`.
11. Espere al cron y luego ejecute `bash -p` para acceder como root.

## English
1. Scan the target with `nmap` to find open ports (80, 1789, 3690).
2. Run `gobuster` on port 80; download the file at `/upload` and read the advice
   about Subversion.
3. Visit `http://<IP>/subversion`; note that authentication is required.
4. Run the Python brute‑force script against `svnuser` to recover the password.
5. Access the SVN repository and retrieve the vulnerable binary.
6. Analyze the binary with Ghidra, locate the buffer overflow and `shell` function.
7. Develop a Python exploit that answers the questions, predicts the pseudorandom
   number, and overflows the buffer to call `shell`.
8. Obtain a shell as the `luigi` user.
9. Check processes and inspect `/etc/crontab`; spot the `tar` job archiving `/home/luigi/*`.
10. Create `--checkpoint*` files and a script to make `/bin/bash` SUID.
11. Wait for cron and run `bash -p` to achieve root.
