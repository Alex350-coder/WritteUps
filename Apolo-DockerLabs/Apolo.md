# ![Apolo CTF](img/Icon.png)

## Introducción

Apolo es un CTF (Capture The Flag) de nivel medio disponible en DockerLabs, considerado uno de los laboratorios más complejos debido a los múltiples retos presentados y las diversas técnicas necesarias para comprometer el sistema de forma progresiva.

---

## Fase 1: Reconocimiento

### Verificación de conectividad

Se inicia el proceso con un ping para verificar la conectividad con el objetivo:

![Verificación de ping](img/Ping.png)

### Escaneo de puertos

Se realiza un escaneo rápido con `Nmap` utilizando el flag `-T4` Sin embargo, en entornos reales se recomienda utilizar `-T2` o inferior para minimizar el ruido generado durante el escaneo:

![Escaneo con Nmap](img/NmapScan.png)

Del análisis se identifica que el puerto 80 ejecuta un servicio HTTP y no se encuentran otros servicios abiertos. Por lo tanto, se procede con una visita de reconocimiento inicial a este servicio.

### Inspección del servicio web

Se accede al servicio HTTP para examinar su contenido:

![Página principal del servicio HTTP](img/Http.png)

Se identifica que la página web simula una tienda de productos "Apple" con diversos apartados relevantes. Para continuar el análisis, se realiza un escaneo con `gobuster` en busca de directorios y archivos ocultos:

![Escaneo con Gobuster](img/GobusterScan.png)

---

## Fase 2: Enumeración y búsqueda de vulnerabilidades

Se identifican varios directorios con estados HTTP 200 y 301. Al investigar estos apartados, se descubren elementos importantes:

### Panel de registro y usuario

Se encuentra un formulario de registro e inicio de sesión:

![Formulario de registro](img/RegisterForm.png)

Tras registrar un nuevo usuario, se observa un panel de usuario con información relevante:

![Panel de usuario genérico](img/GenericUserPanel.png)

Se destaca el campo ID con valor 4 para el nuevo usuario, lo que indica la existencia de múltiples usuarios registrados. Esta observación sugiere una posible vulnerabilidad de **inyección SQL (SQLi)**.

### Panel de búsqueda de productos

Se descubre un panel con un formulario de búsqueda:

![Panel de carrito](img/CartPanel.png)

---

## Fase 3: Explotación de inyección SQL

### Identificación de la vulnerabilidad

Se utiliza `sqlmap` para verificar la existencia de vulnerabilidades SQL en el formulario de búsqueda:

![Base de datos identificadas por SQLMap](img/SqlMapDBs.png)

Se confirma la existencia de una vulnerabilidad de tipo **Union-based SQL injection**. Se procede a extraer información de la base de datos:

![Tabla de productos extraída](img/ProductsTableSqlMap.png)

![Tabla de usuarios extraída](img/UsersTableSQLmap.png)

Se obtienen dos tablas cruciales: la de productos y la de usuarios. La tabla de usuarios contiene credenciales administrativas. Se destaca que la contraseña está hasheada. Se utiliza un servicio en línea para desencriptarla:

![Tabla de usuarios con hashes](img/HashUsersTable.png)

---

## Fase 4: Acceso como administrador

Se inicia sesión con las credenciales de administrador obtenidas. En el panel de usuario se observa una nueva opción:

![Panel de administración](img/AdministrationPanel.png)

Se accede al panel de administración que ofrece varias opciones de interacción:

![Nuevas opciones del panel de administración](img/NewOptionAdminPanel.png)

---

## Fase 5: Bypass de restricciones y ejecución de código

### Intento de carga de archivos

Se identifica un apartado de configuración que permite cargar archivos. Se intenta subir un archivo web shell en PHP:

![Interfaz de carga de archivos](img/UploadFile.png)

El sistema rechaza archivos con extensión `.php`:

![Error: extensión no permitida](img/FileNotAllowed.png)

### Bypass con extensión alternativa

Se intercepta la solicitud con BurpSuite y se envía al repetidor para probar extensiones alternativas:

![Petición en Burp Repeater](img/BurpRepeater.png)

Se logra éxito al utilizar la extensión `.phtml`:

![Carga exitosa con extensión .phtml](img/RepeaterSuccess.png)

### Interfaz para interactuar con la shell web

Se desarrolla una pequeña interfaz gráfica para facilitar la interacción con la shell web:

![Interfaz de shell web](img/ShellInterface.png)

Se verifica la funcionalidad ejecutando el comando `id`:

![Verificación de la shell con el comando id](img/ShellId.png)

---

## Fase 6: Reverse shell

Para mejorar la interacción, se establece una reverse shell. Se coloca un listener en el puerto `4444` en la máquina atacante utilizando `nc` y se ejecuta un comando para mandar la señal a dicho puerto:

![Ejecución de reverse shell](img/reverseShell.png)

Se obtiene una conexión más intuitiva, es buena practica configurar el TTY, lo cual es realizado con exito, en ello se usa el comando `find` para buscar binarios importantes, se desprenden binarios comunes, la mayoria sin reelevancia.

![Conexión de reverse shell establecida](img/FindReverseShell.png)

---

## Fase 7: Escalación de privilegios - Primer nivel

### Enumeración de usuarios

Se examina el archivo `/etc/passwd` para identificar usuarios del sistema.

Se identifica un usuario llamado `Luisillo` con credenciales hasheadas. Se realiza un ataque de fuerza bruta contra el comando `su` utilizando un script y el diccionario `rockyou.txt`.

Se inicia un servidor Python en el puerto 8081 para descargar el script y el diccionario:

![Servidor Python iniciado](img/BeginServerPython.png)

Se otorgan permisos de ejecución al script y se ejecuta:

![Ataque de fuerza bruta contra el comando su](img/BruteForceSuComand.png)

**Nota:** Se modifica el script para omitir la salida de intentos fallidos, mejorando la presentación, solo se elimino una linea `echo` en el bucle, quendado de la siguiente forma:

![Modificaciones al script de fuerza bruta](img/ScriptChanges.png)

Se obtiene la contraseña de \Luisillo\:

![Contraseña de Luisillo descubierta](img/FindPasswordSell.png)

### Pruebas de permisos del usuario Luisillo

Se inician sesión con el usuario y se prueban sus capacidades:

![Acciones disponibles para el usuario Luisillo](img/luisActions.png)

---

## Fase 8: Escalación de privilegios - Nivel root

### Acceso al archivo shadow

Se identifica que el usuario `Luisillo` pertenece al grupo `shadow`, lo que permite acceder a los hashes de contraseñas del sistema:

![Contenido del archivo shadow](img/ShadowCat.png)

Se extraen los hashes de las credenciales. Se utiliza `John the ripper` con el diccionario `rockyou.txt` para desencriptarlos:

![Hashes de contraseña para root](img/PasswdHashForRoot.png)

Se obtiene exitosamente la contraseña de root. Se inicia sesión con estas credenciales:

![Sesión iniciada como root](img/root.png)

**Finalmente, Root!!!**

---

## Resumen de vulnerabilidades encontradas

### 1. Inyección SQL (SQL Injection)

Se identificó una vulnerabilidad de **Union-based SQL injection** en el formulario de búsqueda de productos, permitiendo la extracción de información confidencial de la base de datos.

**Recomendaciones de mitigación:**
- Utilizar consultas preparadas (prepared statements) con parámetros vinculados para evitar que la entrada del usuario sea interpretada como código SQL.
- Implementar validación estricta de entrada en el servidor, restringiendo caracteres especiales y patrones sospechosos.
- Aplicar el principio de menor privilegio en las credenciales de base de datos, asignando permisos limitados específicamente necesarios para cada operación.

### 2. Carga de archivos maliciosos

Se permitió la carga de archivos en el servidor, facilitando la subida de shells web y código malicioso sin validación adecuada de tipo de archivo.

**Recomendaciones de mitigación:**
- Implementar validación de tipos de archivo tanto en el cliente como en el servidor, verificando extensiones y tipos MIME permitidos.
- Almacenar archivos subidos en directorios fuera del webroot para evitar la ejecución accidental de código.
- Renombrar archivos subidos a nombres aleatorios para prevenir el acceso directo y ocultarlos del usuario final.
- Deshabilitar la ejecución de scripts en los directorios de almacenamiento configurando adecuadamente el servidor web.

### 3. Reverse shell y ejecución remota de código (RCE)

La capacidad de ejecutar código malicioso permitió obtener una sesión interactiva en el servidor con privilegios del usuario web.

**Recomendaciones de mitigación:**
- Ejecutar la aplicación web con un usuario del sistema de bajos privilegios, aislado del resto del sistema.
- Implementar un WAF (Web Application Firewall) para detectar y bloquear patrones de ejecución remota de código.
- Monitorear activamente el comportamiento del proceso de la aplicación, alertando sobre conexiones de red sospechosas o comandos del sistema anómalos.

### 4. Contraseñas débiles y almacenamiento inseguro

Las credenciales de usuario se encontraban hasheadas débilmente, permitiendo su recuperación mediante diccionarios comunes.

**Recomendaciones de mitigación:**
- Utilizar funciones de hashing modernas y resistentes como bcrypt, scrypt o Argon2 en lugar de algoritmos débiles como MD5 o SHA1.
- Implementar un mecanismo de "salting" único para cada contraseña, aumentando la complejidad del ataque por fuerza bruta.
- Establecer políticas de contraseña robustas que requieran longitud mínima, complejidad y cambios periódicos.

### 5. Pertenencia a grupos privilegiados

El usuario `Luisillo` era miembro del grupo `shadow`, permitiéndole acceder a hashes de credenciales del sistema.

**Recomendaciones de mitigación:**
- Auditar regularmente la membresía de grupos del sistema, asegurando que los usuarios tengan los mínimos privilegios necesarios.
- Implementar una política de separación de privilegios estricta, limitando el acceso a archivos sensibles como `/etc/shadow`.
- Utilizar control de acceso basado en roles (RBAC) para definir claramente qué grupos pueden acceder a información sensible.

---