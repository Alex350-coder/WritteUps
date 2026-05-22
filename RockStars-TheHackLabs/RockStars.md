# Rock Stars

![alt text](img/icon.png)

## Resumen ejecutivo

Este laboratorio de hacking práctico se centra en la explotación de una vulnerabilidad de inclusión local de archivos (LFI) y en la escalada de privilegios mediante una configuración insegura de `sudo` y manipulación de la ruta de importación de Python. El objetivo es comprometer primero la cuenta `shark`, luego `loseey`, después `username3`, y por último obtener acceso root.

## Recolección inicial

The hacker Labs.

El laboratorio comienza con un escaneo de nmap revelando abiertos los puertos 22 y 80:

![alt text](img/1-nmap.png)

Posteriormente se realiza un escaneo con gobuster que revela ciertos directorios expuestos:

![alt text](img/2-gobuster.png)

La mayoría de ellos en blanco, pero index.php muestra la frase "Yo no soy tu marido". 

![alt text](img/3-http.png)

## Vulnerabilidad LFI

Al no haber más pistas se procede con un ataque de fuzzing al método POST de la página que revela que el parámetro `backdoor` permite aprovechar una vulnerabilidad LFI. 

![alt text](img/4-ffuf.png)

Para corroborarlo se realiza un curl con el parámetro `-d` y efectivamente la vulnerabilidad existe:

![alt text](img/5-etcPasswd.png)

Posteriormente se prueba leer el archivo `db.php`, el cual en el navegador no mostraba ninguna información, pero se prueba si de esta forma, con método POST sí retorna algo:

![alt text](img/6-sharkCredentials.png)

Efectivamente, retorna credenciales de un usuario shark.

## Acceso inicial

Posteriormente se inicia sesión con dicho usuario en el ssh:

![alt text](img/7-sharkSsh.png)

Al verificar con `sudo -l`, se descubre que este usuario puede usar un archivo `bof` como `wvverez` sin contraseña:

![alt text](img/8-sudoShark.png)

Dicho archivo se encuentra en el directorio de shark:

![alt text](img/9-bof.png)

Al revisar con más detenimiento, se descubre que el dueño del documento es shark, por lo que sobreescribimos el archivo para que solo retorne una shell manteniendo los permisos de wvverez el cual lo está ejecutando:

![alt text](img/9-sshWvverez.png)

## Escalada de privilegios local

Al revisar el directorio de este nuevo usario se identifica un archivo zip:

![alt text](img/10-wvverezZIP.png)

Al intentar descomprimirlo se da con el caso de que requiere contraseña, pero también, nos brinda información del archivo de salida, el cual es de contraseñas de algún usuario:

![alt text](img/11-rubialesUnzip.png)

Para poder descomprimirlo primero se envía a la máquina atacante, para lo cual se inicia un servidor en la máquina víctima y se obtiene con wget:

![alt text](img/12-wget.png)

Posteriormente, se obtiene el hash original del archivo y con eso se ejecuta john para realizar un ataque de fuerza bruta con el diccionario rockyou.txt, obteniendo la contraseña:

![alt text](img/13-jhonRubiales.png)

Posteriormente se procede a descomprimir y efectivamente parecen ser varias credenciales:

![alt text](img/15-unzipRubiales.png)

## Enumeración adicional

Se procede a listar todos los usuarios del sistema:

![alt text](img/14-usersSH.png)

Con los posibles ususarios se intenta hacer ataques de fuerza bruta a los otros dos usuarios posibles aparte de root, obteniendo credenciales para loseey:

![alt text](img/16-hydra.png)

Con esto, se procede a iniciar sesión:

![alt text](img/17-sshLosely.png)

Al revisar el directorio principal de este nuevo usuario se procede a ver lo que hay en su directorio principal, revelando un archivo `rubiales.py`, el cual importa un módulo que permite ver la memoria del sistema total y la restante:

![alt text](img/18-rubialesPy.png)

Al revisar con `sudo -l`, se ve que este binario puede ser usado como el usuario username3 sin contraseña.

![alt text](img/19-sudoLoseey.png)

Con ello se abre un vector de ataque claro. En Python el `import` suele priorizar las direcciones o el `PATH` antes que los módulos generales del lenguaje. Es así que si se crea un archivo `psutil.py` el `import` se referirá a dicho archivo y no a la librería ya establecida. Al ejecutarse el otro archivo, se ejecutará por tanto el código malicioso del módulo creado manualmente, obteniendo así una shell como username3:

![alt text](img/20-sshUsername3+.png)

En este apartado encontramos la primera flag del laboratorio:

![alt text](img/21-fisrtFlag.png)

## Acceso root

En este nuevo usario se verifica con `sudo -l`, y puede ejecutar `bsh` como root:

![alt text](img/23-sudoUsername3.png)

Este archivo abre una especie de shell con java, de acciones limitadas:

![alt text](img/22-bsh.png)

El vector de ataque en esta parte está claro, se tiene que usar esta shell para abrir una shell como root. Sin embargo esto no es posible directamente, por lo que se procede por algo alterno. Como se puede ejecutar comandos en este ambiente como root, se decide liberar el permiso de abrir una shell root con `suid` a cualquier usuario, dando así a username3 la posibilidad de escalar a root directamente:

![alt text](img/root.png)

Funciona, somos root!!

Se busca la flag root en su directorio:

![alt text](img/flagRoot.png)

## Recomendaciones

- Validar y sanear estrictamente los parámetros de entrada para evitar vulnerabilidades de inclusión local de archivos. Limitar las rutas accesibles y deshabilitar la carga de archivos arbitrarios.
- Eliminar cualquier archivo de configuración sensible expuesto en el directorio web, como `db.php`, y aplicar controles de acceso adecuados para credenciales y datos de conexión.
- Restringir los permisos de `sudo` y evitar permitir que un usuario ejecute binarios o scripts como otro usuario sin una lista explícita de comandos seguros.
- Evitar almacenar archivos comprimidos protegidos con contraseñas débiles o accesibles al usuario. Implementar cifrado fuerte y garantizar que los datos sensibles no estén expuestos de forma innecesaria.
- Proteger los archivos de Python y limitar la búsqueda de módulos en directorios no confiables para evitar ataques de path hijacking. Usar importaciones absolutas y permisos adecuados en los directorios de trabajo.
- Auditar y eliminar privilegios innecesarios de `bsh` y otros comandos con capacidad root. No otorgar shells o utilidades de escalada de privilegios sin controles de seguridad estrictos.
