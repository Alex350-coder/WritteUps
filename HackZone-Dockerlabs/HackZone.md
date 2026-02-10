
# HackZone

Este documento describe de forma concisa el recorrido realizado en el CTF "HackZone", centrado en la explotación de una carga útil (webshell), la exposición de credenciales en archivos accesibles y la escalada de privilegios posterior.


## Resumen del escenario

1. Reconocimiento inicial mediante escaneo de puertos y enumeración de servicios.

	- Se identificaron tres puertos relevantes: `22` (SSH), `53` (DNS) y `80` (HTTP).

	![alt text](Img/nmap.png)

2. Enumeración DNS

	- Se intentó una transferencia de zona (zone transfer) en el servicio DNS, obteniendo información útil para la enumeración de nombres y hosts.

	![alt text](Img/dig.png)

	- Entre los resultados, se detectó la presencia de un usuario potencial denominado `mrRobot`.

3. Reconocimiento web

	- La búsqueda de directorios con `gobuster` reveló rutas adicionales una vez que se especificó el dominio del laboratorio (`hackzones.hl`).

	![alt text](Img/gobuster.png)

	- Se identificaron recursos relacionados con gestión de archivos: un directorio `uploads` y el script `upload.php` encargado de procesar las cargas.

4. Interacción con la aplicación web

	- Al revisar `dashboard.html` se observó una funcionalidad que permite subir archivos desde el perfil del usuario.

	![alt text](Img/AdminDashboard.png)

	- La funcionalidad de carga fue abusada para subir un archivo PHP con una webshell.

	![alt text](Img/upload.png)
	![alt text](Img/UploadShellSucces.png)

5. Obtención de shell reversa

	- Tras ejecutar la webshell desde el servidor, se estableció una conexión reversa hacia la máquina atacante (se debe tener un listener `nc -lvnp <puerto>` en la máquina atacante).

	![alt text](Img/reverseShell.png)

	- Una vez obtenida la sesión, se mejoró la terminal (TTY) para interacción completa.

	![alt text](Img/tty.png)

6. Credenciales y movimiento lateral

	- Se localizaron credenciales que permitieron acceder al panel administrador, lo que evidencia una configuración insegura de autenticación y gestión de secretos.

	- La revisión de `/etc/passwd` confirmó la existencia del usuario `mrrobot`.

	![alt text](Img/usersEtcPasswd.png)

	- Se encontró un script que concatena valores hexadecimales para formar una cadena secreta (posible contraseña).

	![alt text](Img/secretSH.png)
	![alt text](Img/catSecret.png)

	- La contraseña derivada permitió iniciar sesión como `mrrobot` mediante `su`.

	![alt text](Img/suMrrobot.png)

7. Acceso a la primera bandera

	- En el directorio del usuario `mrrobot` se localizó la primera bandera.

	![alt text](Img/flag1.png)

8. Revisión de privilegios y escalada

	- Con `sudo -l` se detectó que el usuario podía ejecutar `cat` con `sudo` sin necesidad de contraseña.

	![alt text](Img/sudo-l.png)

	- A pesar de esta posibilidad, la lectura directa de la segunda bandera fue bloqueada por una medida de protección (imagen ilustrativa).

	![alt text](Img/think.png)

	- Tras una inspección adicional, se identificó un archivo en `/opt` que contenía credenciales con privilegios elevados (root).

	![alt text](Img/RootCredentials.png)

	- El uso de dichas credenciales permitió obtener acceso root y leer la segunda bandera.

	![alt text](Img/rootSecondFlag.png)


## Análisis de vulnerabilidades encontradas

- Carga de archivos sin validación suficiente: la aplicación permitía subir scripts ejecutables y almacenarlos en un área accesible por el servidor web.
- Exposición de credenciales en archivos accesibles: credenciales sensibles fueron dejadas en texto claro en ubicaciones consultables por usuarios con acceso a la máquina.
- Transferencias de zona DNS no limitadas: la zona DNS permitió extracción de información a hosts no autorizados.
- Configuración de `sudo` insegura: permisos `NOPASSWD` sobre comandos básicos (`cat`) permiten lecturas de archivos sensibles si se aprovecha un vector adecuado.
- Autenticación y control de acceso insuficientes: existencia de credenciales reutilizables y paneles accesibles denota fallas en la gestión de acceso.

## Recomendaciones y medidas de mitigación (5 acciones clave)

1. Hardenizar las cargas de archivos

	- Implementar validación estricta de tipo y extensión, renombrado seguro, almacenamiento fuera del webroot y comprobación de contenido (no permitir PHP ni otros scripts ejecutables). Rechazar archivos potencialmente peligrosos y usar almacenamiento con permisos mínimos.

2. Eliminar y proteger credenciales en disco

	- Retirar credenciales en texto claro del sistema de archivos; utilizar soluciones de gestión de secretos (vaults) y controles de acceso basados en identidad. Implementar rotación periódica de credenciales y revisión de artefactos en `/opt` u otros directorios.

3. Restringir transferencias de zona y endurecer DNS

	- Deshabilitar transferencias de zona a servidores no autorizados y aplicar controles de acceso en el servidor DNS. Considerar DNSSEC y registros mínimos expuestos públicamente.

4. Aplicar principio de menor privilegio y revisar `sudoers`

	- Evitar `NOPASSWD` en `sudo`, limitar comandos que pueden ejecutarse y auditar las reglas de `sudo`. Implementar controles adicionales antes de permitir lecturas de ficheros sensibles.

5. Monitoreo, detección y segmentación de red

	- Implementar registro centralizado, alertas por actividad inusual (subida de archivos, shells reversas) y segmentación de la red para aislar servicios públicos de sistemas con información sensible. Habilitar alertas para intentos de transferencias de zona y accesos a rutas de administración.


## Conclusión

El laboratorio demuestra vectores clásicos de compromiso: cargas inseguras, exposición de credenciales y configuraciones excesivamente permisivas. La remediación requiere cambios combinados en la aplicación web, la gestión de credenciales y la configuración de la infraestructura.
