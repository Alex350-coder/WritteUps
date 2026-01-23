# WhereIsMyWebShell - Writeup

![WhereIsMyWebShell](Img/icon.png)

## 📋 Descripción General

Este laboratorio proporciona una oportunidad valiosa para desarrollar habilidades en técnicas de fuzzing y en la ejecución de shells inversas (reverse shells). A través de esta máquina, se demuestra la importancia crítica de identificar y explotar vulnerabilidades en aplicaciones web que permiten la ejecución de comandos no autorizada.

---

## 🔍 Fase 1: Reconocimiento e Identificación de Servicios

### Escaneo de Puertos con Nmap

El proceso de resolución comienza con un escaneo exhaustivo de puertos utilizando Nmap para identificar los servicios disponibles en la máquina objetivo:

![Nmap Scan](Img/nmapscan.png)

Del escaneo realizado se desprende que únicamente existe un servicio activo en el puerto 80, correspondiente a un servidor HTTP. Este descubrimiento simplifica el enfoque de ataque, permitiendo concentrar los esfuerzos en la exploración de la aplicación web.

---

## 🌐 Fase 2: Exploración de la Aplicación Web

### Acceso Inicial

Se procede a visitar la página web en la dirección de la máquina objetivo:

![HTTP Initial Access](Img/http.png)

La página principal contiene información general sin relevancia aparente. Sin embargo, al desplazarse hacia la parte inferior de la página, se identifica una sección que proporciona una pista crucial para la resolución del laboratorio:

![HTTP Advice](Img/HttpAdvice.png)

Esta pista menciona la existencia de un secreto ubicado en el directorio `/tmp`. Los intentos directos de acceso a esta ruta no producen resultados positivos, por lo que se procede con técnicas más sofisticadas.

---

## 🔎 Fase 3: Descubrimiento de Directorios y Archivos

### Fuzzing con Gobuster

Se realiza un ataque de fuzzing utilizando Gobuster para identificar directorios y archivos ocultos en el servidor web:

![Gobuster Results](Img/Gobuster.png)

El escaneo revela dos hallazgos importantes:

1. **Documento con código 500**: Indica un error en la ejecución del servidor, sugiriendo que existe un archivo que intenta ejecutarse pero genera una excepción.
2. **Archivo HTML adicional**: Muestra un mensaje de advertencia que confirma la presencia de una shell web que debe ser descubierta.

### Análisis de Parámetros

La segunda observación proporciona información crítica: la aplicación web funciona mediante parámetros en la URL, lo que la hace potencialmente vulnerable a ataques de fuzzing paramétrico.

![Warning Page](Img/warningPage.png)

---

## 🎯 Fase 4: Fuzzing Paramétrico

### Identificación del Parámetro Vulnerable

Se ejecuta un ataque de fuzzing contra el archivo `shell.php` utilizando la herramienta FFuz para identificar parámetros válidos:

![FFuz Execution](Img/ffuz.png)

Los resultados demuestran que únicamente el parámetro `parameter` produce respuestas significativas. Cabe destacar que este parámetro fue identificado tras múltiples intentos utilizando diferentes metodologías de ataque, siendo este el único que permitió la ejecución de comandos.

### Validación de Ejecución de Comandos

Se procede a validar si el parámetro identificado permite la ejecución de comandos del sistema. Se utiliza el comando `whoami` para verificar la capacidad de ejecución:

![Parameter Testing](Img/CheckingTheParameter.png)

La ejecución exitosa del comando `whoami` confirma que se ha identificado una vulnerabilidad que permite la ejecución remota de comandos (RCE - Remote Code Execution). Sin embargo, se observa que la shell disponible presenta restricciones significativas, permitiendo únicamente la ejecución de comandos básicos como `ls` y `pwd`.

---

## 🚀 Fase 5: Escalada de Privilegios mediante Reverse Shell

### Limitaciones de la Shell Web

Debido a las restricciones de la shell web identificada, se requiere el establecimiento de una conexión inversa (reverse shell) para obtener mayor control sobre el sistema y ejecutar comandos más complejos.

### Establecimiento de la Reverse Shell

Se prepara el sistema atacante abriendo un puerto de escucha:

```bash
nc -lvnp 4444
```

Desde la aplicación web, se ejecuta una instrucción adaptada para establecer una conexión inversa hacia la máquina atacante:

![Reverse Shell Adaptation](Img/AdaptationOfReverseShell.png)

La instrucción original fue modificada para garantizar su correcta ejecución a través de la URL y el parámetro vulnerable identificado anteriormente.

![Reverse Shell Established](Img/ReverseShell.png)

Una vez establecida la conexión inversa, se obtiene una shell más potente que permite la ejecución de comandos sin las restricciones previas.

---

## 📂 Fase 6: Búsqueda de Credenciales y Archivos Sensibles

### Retorno a la Pista Inicial

Habiendo obtenido control sobre el sistema mediante la reverse shell, se retorna a la pista proporcionada en la página inicial y se procede a explorar el directorio `/tmp`:

```bash
ls -la /tmp
```

Este comando, con la opción `-la` para visualizar archivos ocultos, revela la presencia de un archivo particularmente interesante: `.secreto.txt`. La nomenclatura con punto inicial indica que se trata de un archivo oculto, lo que explica por qué no fue identificado en exploración previa.

### Extracción de Credenciales

Se examina el contenido del archivo descubierto:

```bash
cat /tmp/.secreto.txt
```

El archivo contiene una contraseña que permite la elevación de privilegios en el sistema.

![Root Access](Img/Root.png)

Utilizando la contraseña obtenida y el comando `su` (switch user), se logra acceder con privilegios de administrador (root), completando exitosamente el laboratorio.

---

## 🛡️ Recomendaciones de Seguridad

El presente laboratorio ilustra vulnerabilidades críticas comúnmente encontradas en aplicaciones web. A continuación, se presentan recomendaciones para mitigar riesgos relacionados con la ejecución remota de comandos y shells inversas:

### 1. **Validación y Sanitización de Entrada**
- Implementar validación exhaustiva de todos los parámetros de entrada recibidos desde el cliente.
- Utilizar listas blancas (whitelists) de valores permitidos en lugar de listas negras.
- Aplicar funciones de sanitización que eliminen caracteres especiales y potencialmente peligrosos.
- Ejemplo en PHP: Utilizar `filter_var()`, `htmlspecialchars()` o bibliotecas especializadas.

### 2. **Prohibición de Funciones Peligrosas**
- Deshabilitar o eliminar el uso de funciones que permitan la ejecución de comandos del sistema:
  - `exec()`, `system()`, `passthru()`, `shell_exec()`, `proc_open()`
  - Si su uso es absolutamente necesario, implementar restricciones y auditoría exhaustiva.

### 3. **Control de Acceso y Autenticación**
- Implementar un sistema robusto de autenticación y autorización.
- Verificar que únicamente usuarios autenticados y autorizados puedan acceder a funcionalidades sensibles.
- Utilizar tokens CSRF (Cross-Site Request Forgery) para proteger contra solicitudes no autorizadas.

### 4. **Principio del Menor Privilegio**
- Ejecutar la aplicación web con el mínimo conjunto de permisos requeridos.
- Evitar ejecutar la aplicación con permisos de administrador (root).
- Crear usuarios específicos con permisos limitados para el servicio web.

### 5. **Implementación de Web Application Firewall (WAF)**
- Desplegar un WAF que detecte y bloquee patrones de ataque comunes.
- Configurar reglas específicas para detectar intentos de inyección de comandos y reverse shells.
- Monitorear y registrar intentos sospechosos para análisis posterior.