# Talent

## Resumen del laboratorio

Este laboratorio comienza con un escaneo de red que permite identificar un servidor HTTP con Wordpress. A partir de este punto se explorarán múltiples vulnerabilidades, desde credenciales débiles hasta extensiones vulnerables y configuraciones permisivas en el sistema de archivos. El objetivo es obtener una cadena de compromiso completa, escalando primero a un usuario con privilegios y, finalmente, a root. Al finalizar se proponen recomendaciones para mitigar cada vector de ataque.

---

## Identificación del objetivo y descubrimiento inicial

El laboratorio comienza con un escaneo de `nmap` que revela el puerto 80 abierto:

![alt text](img/1-nmap.png)

Posteriormente se visita dicho puerto y se identifica una página de Wordpress:

![alt text](img/2-http.png)

### Paneles disponibles

Se revisan los distintos paneles disponibles: registro de usuarios (función inhabilitada), formulario de inicio de sesión, uno para errores de login y finalmente uno para mostrar el perfil actual:

![alt text](img/3-registrationpanel.png)

![alt text](img/4logInPanel.png)

![alt text](img/5-ErrorLogInPanel.png)

![alt text](img/6-ProfilePanel.png)

## Autenticación inicial y acceso administrativo

Por metodología personal, probé con el usuario `admin` y una contraseña genérica básica, lo cual funcionó. Esto demuestra una vulnerabilidad grave en un entorno real: credenciales por defecto o débiles permitiendo acceso administrativo.

Con esto se obtiene acceso al dashboard del administrador de Wordpress:

![alt text](img/7-AdminDashBoard.png)

## Explotación de la extensión vulnerable

Al navegar en este dashboard se identifica la extensión **Hello Dolly**; esta es conocida por ser bastante vulnerable. Se utiliza el script de Pentest Monkey para obtener una reverse shell, el cual viene incluido en la mayoría de máquinas Kali:

Ruta usual: `/usr/share/webshells/php/php-reverse-shell.php`

![alt text](<img/8-PentestMonkeyPhpReverseShell copy.png>)

> **Nota:** recordar que se debe especificar al inicio del script el puerto donde se quiere mandar la reverse shell y su respectiva IP de máquina atacante.

Este script debe ser insertado al inicio del código de la extensión (para editarla entrar al apartado de Tools > Editor de plugins) sin borrar el contenido usual de la misma, ya que de ser el caso la extensión se elimina. Poner en escucha el puerto especificado al inicio del script en la máquina atacante con `nc -lnvp PORT` y verificar. En este caso es un éxito:

![alt text](img/9-ReverseShell.png)

## Estabilización del acceso y enumeración interna

Se le hace **TTY** a la máquina para tener un entorno más adecuado:

![alt text](img/10-TYY.png)

Se lee el archivo `/etc/passwd` para ver qué otros usuarios existen:

![alt text](img/11-Users.png)

Y al revisar el directorio `/home` se encuentra la primera flag:

![alt text](img/12-FirstFlag.png)

También se encuentra un archivo interesante en `/opt` llamado `backup`:

![alt text](img/13-OptDirectory.png)

![alt text](img/14-catBackup.png)

## Escalado de privilegios mediante sudo

Con esto se verifican permisos con `sudo -l`:

![alt text](img/13-Sudo-lWWDATA+.png)

Donde se observa que se puede ejecutar `python3` sin contraseña (por algún motivo el módulo no está en el entorno de Docker).

Para resolverlo, se instala Python en el contenedor:

1. `sudo docker ps -a` 
2. `sudo docker exec -it <ID_DOCKER> bash -c "apt update && apt install python3 -y"`

Luego de ello se ejecuta Python como `bobby` para abrir una shell:

```bash
sudo -u bobby /usr/bin/python3 -c 'import os; os.execl("/bin/bash", "bash")'
```

![alt text](img/15-ExecPython3.png)

Con ello se abre una shell como este usuario, y se revisan inmediatamente sus privilegios con `sudo -l`:

![alt text](img/16-sudo-L.png)

El resultado indica que este usuario puede ejecutar el script `backup.py` como sudo sin contraseña; al hacerlo se hace la recompilación de archivos. En un inicio se pensó en leer el archivo `/etc/shadow` pero este no mostró nada para escalar de privilegios. Se revisa el directorio `/opt` y se verifica algo que había pasado desapercibido: este puede ser editado por cualquiera, junto con su contenido.

![alt text](img/17-RevicionBackupOpt.png)

## Compromiso completo: obtención de root

Por lo que con esto se procede a borrar el actual archivo `backup.py`, y se crea uno nuevo con el mismo nombre pero que solo ejecuta una bash como root:

![alt text](img/root.png)

Funciona, ya somos root!!!!

## Recomendaciones

- **Contraseñas débiles/por defecto:** Implementar políticas de contraseñas robustas y forzar el cambio de credenciales administradas. Utilizar mecanismos de autenticación multifactor.
- **Extensión vulnerable (Hello Dolly):** Mantener plugins y extensiones siempre actualizados; eliminar los que no sean necesarios y utilizar listas de control de seguridad para plugins.
- **Editor de plugins alcanzable desde web:** Restringir las capacidades de edición de código directamente desde el dashboard, aplicar validaciones y sanitización, o deshabilitar completamente estas funciones en producción.
- **Perfiles sudo con permisos amplios:** Revisar y limitar las reglas de `sudoers`, evitando permisos genéricos como `NOPASSWD: python3`. Usar herramientas como `sudoedit` y listas blancas forzadas.
- **Directorios escribibles por cualquier usuario (/opt):** Corregir permisos de directorios críticos, asegurando que sólo administradores tengan derechos de escritura.


