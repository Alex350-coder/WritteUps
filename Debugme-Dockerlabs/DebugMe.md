# DebugMe - Laboratorio de Hacking CTF

![alt text](img/icon.png)

## Resumen Ejecutivo

El laboratorio DebugMe es un entorno de práctica para Capture The Flag (CTF) que simula un escenario de penetración realista. El objetivo es obtener acceso de nivel root mediante la combinación estratégica de técnicas de reconocimiento, explotación de vulnerabilidades conocidas, ataques de fuerza bruta, escalada de privilegios y manipulación de procesos del sistema. Este ejercicio proporciona aprendizaje práctico sobre múltiples vectores de ataque comunes en infraestructuras reales.

---

## Fase 1: Reconocimiento y Enumeración

### 1.1 Escaneo de Servicios (Nmap)

Se inicia el laboratorio con un escaneo de puertos mediante Nmap, que es el procedimiento estándar en cualquier evaluación de seguridad. El escaneo reveló la existencia de tres servicios activos en los puertos 22 (SSH), 80 (HTTP) y 443 (HTTPS).

![alt text](img/1-nmap.png)

### 1.2 Descubrimiento de Directorios (Gobuster)

Posteriormente se ejecuta un escaneo de directorios utilizando Gobuster. Este escaneo identificó un punto de interés crítico: el archivo **info.php**, que no debería estar accesible públicamente.

![alt text](img/2-gobuster.png)

---

## Fase 2: Análisis de Información Sensible

### 2.1 Divulgación de Información en info.php

Al acceder a info.php se observa información que ordinariamente debería estar protegida. Sin embargo, durante el análisis se descubre un elemento aún más crítico: la presencia de la librería **ImageMagick** siendo utilizada por el servidor.

![alt text](img/3-infoPHP.png)

---

## Fase 3: Explotación de Vulnerabilidad en ImageMagick (CVE-2022-44268)

### 3.1 Identificación de la Vulnerabilidad

Se investigó y se identificó el **CVE-2022-44268**, una vulnerabilidad de Local File Inclusion (LFI) presente en la versión específica de ImageMagick empleada por el servidor. Esta vulnerabilidad permite extraer archivos del sistema manipulando metadatos de imágenes.

![alt text](img/4-CVEImagick.png)

### 3.2 Preparación del Exploit

Para ejecutar el exploit, se procedió a:

1. Clonar el repositorio de explotación correspondiente mediante `git clone URL`
2. Utilizar el módulo Python disponible en el repositorio para generar una imagen especialmente crafteada
3. La imagen generada aprovecha la vulnerabilidad para extraer archivos críticos del sistema, en este caso `/etc/passwd`

El comando utilizado fue:

```bash
python3 generate.py -f /etc/passwd -o passwd.png
```

### 3.3 Carga y Descarga del Payload

El archivo PNG resultante se cargó utilizando el servicio HTTP disponible en el puerto 80.

![alt text](img/5-UploadImage.png)

Una vez completada la carga, la imagen debe ser descargada del servidor y analizada utilizando herramientas de procesamiento de imágenes:

```bash
identify -verbose DownloadImage.png
```

![alt text](img/5-DataOfImage.png)
![alt text](img/6-etcPasswd.png)

### 3.4 Decodificación de Datos

La información extraída se encontraba codificada en formato hexadecimal. Se utilizó Python u otras herramientas para convertir los datos a texto legible:

![alt text](img/7-HexadecimalToNormal.png)

---

## Fase 4: Ataque de Fuerza Bruta a Credenciales SSH

### 4.1 Identificación de Usuarios Válidos

Del análisis del archivo `/etc/passwd`, se identificaron dos usuarios potenciales capaces de ejecutar shell bash: **lenam** y **application**.

### 4.2 Ataque de Diccionario

Dado que no había vectores alternativos disponibles, se ejecutó un ataque de fuerza bruta contra ambos usuarios utilizando herramientas como Hydra. El ataque fue exitoso contra el usuario **lenam**, revelando sus credenciales.

![alt text](img/8-hydra.png)

---

## Fase 5: Acceso Inicial y Reconocimiento de Escalada

### 5.1 Conexión SSH

Con las credenciales obtenidas, se estableció una sesión SSH con el usuario lenam.

### 5.2 Análisis de Permisos Sudo

Se ejecutó `sudo -l` para enumerar las capacidades administrativas disponibles para el usuario. Se descubrió que el usuario tiene permiso para ejecutar `/bin/kill` como root sin requerir contraseña.

![alt text](img/10-sshLenam.png)

### 5.3 Enumeración de Servicios Locales

Se realizó un análisis exhaustivo de puertos y servicios locales ejecutando:

```bash
netstat -ano
```

![alt text](img/11-netstat.png)

Este análisis reveló la existencia de dos servicios escuchando en interfaces locales:
- **127.0.0.1:8000**
- **127.0.0.1:9000**

Se verificó cada servicio mediante Curl, obteniendo respuesta positiva en **localhost:8000**, donde se confirmó la presencia de un servicio Node.js ejecutándose.

![alt text](img/12-NodeProcess.png)

---

## Fase 6: Escalada de Privilegios mediante Manipulación de Procesos

### 6.1 Vector de Ataque

Se formuló un vector de ataque estratégico consistente en:

1. Utilizar el permiso sudo sobre `/bin/kill` para detener el proceso de Node.js
2. Capturar la shell de debug de Node.js que se activa durante el reinicio
3. Desde la shell de debug, ejecutar una reverse shell como root

### 6.2 Identificación del PID del Proceso Node.js

Se identificó el identificador de proceso (PID) del servicio Node.js ejecutándose como root:

```bash
ps aux | grep node
```

![alt text](img/13-Idroot.png)

### 6.3 Ejecución de Comando con Privilegios Elevados

Se elaboró y ejecutó un comando utilizando `exec` para enviar una reverse shell con los privilegios de root. 

**Nota importante:** Antes de ejecutar este comando, se debe configurar un listener en la máquina atacante:

```bash
nc -lnvp PORT
```

![alt text](img/15-exectToRootSheel.png)

### 6.4 Acceso Root Obtenido

La ejecución fue exitosa, resultando en una shell interactiva con privilegios de administrador:

![alt text](img/16-root.png)

---

## Resumen de Éxito

Se ha completado exitosamente la cadena de explotación, obteniendo acceso de nivel root al sistema objetivo. Este laboratorio demuestra la criticidad de implementar múltiples capas de seguridad y la importancia de la defensa en profundidad.

---

## Vulnerabilidades Identificadas y Recomendaciones de Mitigación

### 1. Divulgación de Información Sensible (info.php)

**Vulnerabilidad:** Archivo que expone información del servidor mediante la función `phpinfo()` o similar.

**Recomendación de Mitigación:** 
- Eliminar completamente archivos de prueba y diagnóstico (info.php, test.php, etc.) de entornos de producción.
- Implementar una política de gestión de archivos que prohibía la inclusión de scripts de diagnóstico en deployments.
- Configurar el servidor web para denegar acceso a ciertos archivos mediante directivas en `.htaccess` o nginx.conf.
- Realizar auditorías periódicas del árbol de directorios accesibles públicamente.

### 2. Local File Inclusion (LFI) vía CVE-2022-44268 en ImageMagick

**Vulnerabilidad:** La versión de ImageMagick utilizada contiene una vulnerabilidad de LFI que permite extraer archivos del servidor a través de metadatos manipulados en imágenes.

**Recomendación de Mitigación:**
- Actualizar inmediatamente ImageMagick a la versión más reciente que incluya el parche de seguridad para CVE-2022-44268.
- Implementar validaciones rigurosas en la carga de archivos: verificar tipos MIME, tamaños máximos y ejecutar análisis de contenido malicioso.
- Ejecutar ImageMagick en un entorno sandboxed o contenedor con permisos limitados.
- Establecer un proceso de gestión de vulnerabilidades que incluya monitoreo de CVEs publicados para bibliotecas críticas.

### 3. Credenciales Débiles y Ataques de Fuerza Bruta

**Vulnerabilidad:** El usuario lenam poseía contraseña débil susceptible a ataques de diccionario.

**Recomendación de Mitigación:**
- Implementar una política de contraseñas robusta que exija mínimo 12 caracteres con complejidad (mayúsculas, minúsculas, números y caracteres especiales).
- Configurar límites de intentos fallidos de login (ej: bloqueo después de 5 intentos) con incremento exponencial de tiempo de espera.
- Implementar autenticación multifactor (MFA) para todos los usuarios, especialmente para acceso SSH.
- Deshabilitar la autenticación por contraseña en SSH en favor de autenticación basada en claves públicas (SSH keys).
- Monitorear y registrar intentos de login fallidos mediante SIEM o sistemas de logging centralizado.

### 4. Configuración Insegura de Permisos Sudo

**Vulnerabilidad:** El usuario lenam tiene permiso para ejecutar `/bin/kill` como root sin contraseña, lo que puede ser explotado para manipular procesos críticos.

**Recomendación de Mitigación:**
- Aplicar el principio de mínimo privilegio: limitar permisos sudo solo a comandos estrictamente necesarios.
- Requerir contraseña incluso para comandos permitidos en sudoers mediante `PASSWD` junto al comando.
- Implementar auditoría detallada de todos los comandos ejecutados con sudo mediante `sudoedit`, `sudo_logsrvd` o similar.
- Revisar periódicamente la configuración de sudoers (archivo `/etc/sudoers`) en búsqueda de permisos excesivos.
- Evitar permitir comandos peligrosos como `/bin/kill`, `/bin/su`, `/bin/bash` sin validaciones adicionales.

### 5. Servicio Node.js Expuesto Localmente como Root

**Vulnerabilidad:** Un servicio crítico Node.js ejecutándose con privilegios root está accesible a través de localhost, facilitando una escalada de privilegios.

**Recomendación de Mitigación:**
- Ejecutar servicios de aplicación con el usuario con mínimos privilegios necesarios, nunca como root.
- Implementar un modelo de contenedor (Docker) o máquina virtual aislada donde la aplicación no requiera acceso root.
- Restringir el acceso a servicios locales mediante firewalls de host (iptables, Windows Firewall) bloqueando solo tráfico de procesos autorizados.
- Implementar API gateway y reverse proxy con autenticación para servicios internos.
- Monitorear procesos en ejecución y alertar sobre servicios inesperados ejecutándose con privilegios elevados.

### 6. Explotación de Manipulación de Procesos y Shells de Debug

**Vulnerabilidad:** La capacidad de terminar procesos root combinada con la disponibilidad de debugging en Node.js permite obtener acceso privilegiado.

**Recomendación de Mitigación:**
- Deshabilitar herramientas de debug y verbose logging en entornos de producción (incluyendo Node.js inspect mode).
- Implementar protección de procesos críticos mediante AppArmor, SELinux o Seccomp que impidan su terminación por usuarios no autorizados.
- Utilizar process managers que reinicien automáticamente servicios sin permitir interacción con debugging.
- Implementar process isolation y containerization para evitar que la terminación de un proceso exponga shells de debugging.
- Aplicar hardening del kernel mediante deshabilitación de ptrace en producción cuando sea posible.

---
