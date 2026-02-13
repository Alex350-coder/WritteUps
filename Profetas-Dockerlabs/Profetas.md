
# **Resumen del laboratorio**

En este laboratorio se realiza un ejercicio de reconocimiento, explotación web y escalada de privilegios en una máquina objetivo. Se identifican servicios HTTP (puerto 80) y SSH (puerto 22), se explotan vulnerabilidades de validación de entradas y manejo de XML (XXE), se obtiene acceso a usuarios locales por SSH y, finalmente, se escala hasta obtener credenciales de root mediante intercambio de archivos (`croc`).

El siguiente documento describe los pasos observados, las pruebas realizadas y recomendaciones para mitigar las vulnerabilidades detectadas.


# **1. Reconocimiento**

Se inicia con un escaneo de puertos que revela los servicios HTTP y SSH en los puertos 80 y 22 respectivamente:

![alt text](img/nmap.png)

Con `gobuster` se enumeran rutas y recursos web relevantes (por ejemplo `upload`, `dashboard`, etc.):

![alt text](img/gobuster.png)


# **2. Exploración de la aplicación web**

Al acceder al servicio HTTP se encuentra una interfaz de inicio de sesión:

![alt text](img/httpBegining.png)

La revisión del código cliente/servidor revela ausencia de validación de entrada en ciertas rutas, lo que permite avanzar sin credenciales y mostrar contenido con nombres (lista de profetas):

![alt text](img/http.png)

Al inspeccionar más a fondo, se descubre un mensaje oculto codificado en Base64 en el cliente:

![alt text](img/hidenMessage.png)

Su decodificación arroja información útil para la investigación:

![alt text](img/hiddenMessageTranslated.png)


# **3. Acceso al panel de administrador mediante inyección SQL**

Con la información previa se continúa hasta un formulario de administración que sí valida formato de usuario:

![alt text](img/adminLogIn.png)

Al identificar que el usuario permitido es `notadmin@gmail.com`, se realiza una prueba de inyección SQL en el campo de contraseña (probado con Burp Suite):

![alt text](img/SLQi.png)

La explotación permitió acceder a un dashboard con capacidad de cargar/procesar archivos XML:

![alt text](img/dashboard.png)


# **4. Explotación XXE y lectura de archivos sensibles**

El procesador XML en el dashboard mostró evidencias de ser vulnerable a XXE (XML External Entity). Aprovechando esto se leyó el archivo `/etc/passwd`, obteniendo una lista de usuarios del sistema, entre ellos `ezequiel` y `jeremias`:

![alt text](img/XXE.png)

![alt text](img/etcPasswd.png)


# **5. Acceso inicial por SSH y explotación de credenciales**

Usando la pista encontrada (`la contraseña es tu usuario`), se intentó autenticación SSH usando el nombre de usuario como contraseña y se consiguió acceso para `jeremias`:

![alt text](img/sshJeremias.png)

Dentro del entorno de `jeremias` se localizó un fichero relacionado con otro usuario; al inspeccionarlo resultó ser un archivo Python compilado (`.pyc`) que se decompiló para recuperar datos:

![alt text](img/PyDocumentDecrypted.png)

De la lectura del código se extrajeron fragmentos `_p1` y `_p2` que, al concatenarse, formaron la contraseña de `ezequiel`. Esto permitió iniciar sesión como `ezequiel`:

![alt text](img/sshEzequiel.png)


# **6. Escalada a root mediante intercambio de archivos (`croc`)**

El usuario `ezequiel` señaló la ruta de un archivo que aparenta contener la contraseña de `root`. Además, se observó que `ezequiel` puede ejecutar `croc` con `sudo` sin contraseña, por lo que se compartió el archivo que contenía la contraseña de root a `jeremias`:

![alt text](img/sendRootPassword.png)

Posteriormente se recibió el archivo y se leyó su contenido, confirmando la credencial de root:

![alt text](img/root.png)

Con esas credenciales se obtiene acceso como `root` y se completa el laboratorio.


# **7. Conclusión**

El flujo de explotación documentado combina fallos clásicos: falta de validación de entrada, inyección SQL, XXE y un control inadecuado de privilegios para utilidades (sudo) que permiten escalada lateral hasta `root`.


# **8. Recomendaciones para mitigación y prevención**

 - **Validación y saneamiento de entradas:** Implementar validación estricta en el servidor (listas blancas, longitud, tipos) y evitar confiar en validación del lado cliente.
 - **Protección contra inyección SQL:** Usar consultas parametrizadas/preparadas y ORM con manejo seguro de parámetros.
 - **Deshabilitar o restringir DTD/XXE:** Configurar los parsers XML para deshabilitar entidades externas y validar/filtrar documentos XML entrantes.
 - **Control de privilegios y sudo:** Revisar la configuración de `sudoers` y evitar conceder ejecución sin contraseña a utilidades que puedan mover o exfiltrar credenciales. Aplicar principio de menor privilegio.
 - **Manejo seguro de credenciales:** Evitar almacenar contraseñas en texto plano; usar vaults/secret managers y registros con control de acceso.
 - **Registro y monitoreo:** Habilitar logging de autenticaciones y cargas de archivos para detectar actividades anómalas.
 - **Revisión de artefactos compilados:** Proteger artefactos (por ejemplo, `.pyc`) que puedan contener secretos; revisar y eliminar información sensible del repositorio.

Estas recomendaciones reducen la probabilidad de explotación combinada y limitan el impacto en caso de compromiso.

