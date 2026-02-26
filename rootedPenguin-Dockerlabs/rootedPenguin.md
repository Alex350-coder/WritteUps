# Rooted Penguin - Análisis Detallado del Laboratorio

## 1. Reconocimiento Inicial

El laboratorio comienza con un escaneo de Nmap que revela dos puertos abiertos: **5173** y **8000**. Aunque inicialmente aparecen como desconocidos, se identifican rápidamente como:
- **Puerto 5173**: Aplicación frontend desarrollada con Vite y React
- **Puerto 8000**: Backend API que sirve como soporte para el frontend

![alt text](img/1-nmap.png)

Posteriormente, se intenta un análisis más profundo con Gobuster, que no produce resultados significativos. Se procede entonces a visitar directamente la aplicación web, descubriendo una página con temática de pingüinos dedicada a brindar apoyo e información relacionada con estas aves.

![alt text](img/2-http.png)

Al explorar el segundo puerto abierto y probar diferentes rutas, se descubre que expone la documentación completa de los endpoints de la API del sistema.

![alt text](img/2-ApisDocs.png)

## 2. Descubrimiento de la Primera Vulnerabilidad: Divulgación de Información

El sistema dispone de funciones de registro e inicio de sesión. Durante la fase de reconocimiento, se prueba con el usuario "admin" para verificar si existe. La respuesta del servidor revela información sensible: **el usuario ya existe**, divulgando la existencia de un usuario con permisos elevados.

![alt text](img/2-adminUser.png)

Esta vulnerabilidad de **divulgación de información** es crítica, ya que permite a un atacante identificar cuentas de administrador válidas sin autorización.

## 3. Análisis del Perfil de Usuario

Se crea un usuario de prueba con credenciales `user:user` y se accede a su perfil:

![alt text](img/3-Profilehttp.png)

En esta sección se observan varios aspectos relevantes:
- El sistema implementa un modelo de roles jerárquicos
- El campo de "dirección" (específicamente la línea 2) es susceptible a **ataques XSS (Cross-Site Scripting)**
- Aunque la vulnerabilidad XSS existe, no es evidente a primera vista

Dado que no hay otros vectores de ataque inmediatos, se procede a analizar el tráfico de red mediante Burp Suite para comprender mejor el flujo de registro e inicio de sesión.

![alt text](img/5-BurpLogIn.png)
![alt text](img/5-BurpLogIn.png)

## 4. Análisis de Tokens JWT

El análisis del tráfico interceptado revela un hallazgo crítico: **el token de sesión (JWT) está expuesto**. Este token es fundamental para escalar privilegios y debe ser analizado.

![alt text](img/6-Token.png)

Se decodifica el JWT y se observa que está firmado, pero el secreto no es inmediatamente aparente. Dado que la aplicación no contiene palabras clave útiles, se realiza un **ataque de fuerza bruta (FB)** utilizando John the Ripper para descubrir la clave secreta:

![alt text](img/7-SecretToken.png)

**¡Éxito!** Se descubre que el secreto es débil. Con este secreto comprometido, se abre un claro vector de ataque.

## 5. Manipulación de Tokens y Escalada de Privilegios

La estrategia es clara: crear un nuevo token JWT firmado con el secreto descubierto, pero con datos de un usuario de nivel superior (en este caso, "admin"). Se utiliza JWT.io para realizar esta manipulación:

**Payload del nuevo token:**
```json
{
    "sub": "admin",
    "role": "admin",
    "exp": 1772137761
}
```

Tras generar el token falsificado, se verifica accediendo a un endpoint protegido (por ejemplo, `/users`), el cual revela todos los usuarios del sistema. Esta es en sí misma otra vulnerabilidad grave: **la exposición no autorizada de datos de usuario**.

![alt text](img/8-AdminTokenVerify.png)

## 6. Inyección de Token en localStorage

El frontend almacena el token de sesión directamente en `localStorage` sin protección adicional. Aprovechando esta debilidad, se interviene el almacenamiento local sustituyendo el token actual por el token de administrador forjado:

![alt text](img/9-SetLocalStorage.png)

Esto permite acceder a la sesión del usuario "admin" sin conocer su contraseña.

## 7. Acceso al Panel Administrativo

Con el token de admin inyectado, se obtiene acceso completo al panel administrativo y al perfil del administrador:

![alt text](img/10-AdminPanel.png)
![alt text](img/11-AdminProfile.png)

En el panel de administración se encuentra un listado de todos los usuarios del sistema. Se identifica un usuario cuyo campo de dirección (vulnerable a XSS) contiene lo que aparenta ser una imagen o ícono, lo que sugiere un posible payload XSS inyectado.

![alt text](img/11-EvhackerProfile.png)

## 8. Prueba de la Vulnerabilidad XSS

Se confirma que el campo de dirección del usuario permite la ejecución de JavaScript a través de payloads de imagen XSS:

![alt text](img/12-XssTesting.png)

Aunque esta vulnerabilidad es real y crítica, resulta tedioso explotarla de forma práctica. Por tanto, se explora una vía alternativa más directa.

## 9. Descubrimiento de Inyección de Comandos

Al revisar detenidamente los endpoints de la API, se identifica que el endpoint **DELETE** es susceptible a **inyección de comandos**. Este endpoint acepta un parámetro `{username}` que no es sanitizado adecuadamente.

Se realiza una prueba inicial con el comando `sleep` para validar la vulnerabilidad:

![alt text](img/14-testDeleteApi.png)

**¡Confirmado!** La inyección de comandos funciona. Ahora se elabora un payload para establecer una reverse shell hacia la máquina atacante en el puerto 4444:

![alt text](img/15-PayloadReverseShell.png)

**⚠️ Notas importantes:**
- El payload debe estar codificado en **Base64** y **URL-encoded**. De lo contrario, puede fallar. (Creedme, se invirtieron más de 60 minutos investigando este comportamiento)
- Asegúrese de tener un listener activo en el puerto 4444 antes de ejecutar el payload

## 10. Obtención de Acceso Root

Finalmente, la reverse shell se conecta exitosamente, revelando un acceso **directo como usuario root**:

![alt text](img/root.png)

## 11. Resumen de Vulnerabilidades Explotadas

1. **Information Disclosure** - Divulgación de usuarios válidos durante el registro
2. **Weak JWT Secret** - Secret débil susceptible a fuerza bruta
3. **Token Manipulation** - Posibilidad de forjar tokens JWT válidos
4. **Insecure localStorage** - Almacenamiento de credenciales sensibles sin protección
5. **Broken Authorization** - Escalada de privilegios mediante tokens manipulados
6. **Reflected XSS** - Cross-Site Scripting en campos de usuario
7. **OS Command Injection** - Inyección de comandos del sistema operativo

---

## 12. Recomendaciones de Mitigación

### 1. Mitigación de Divulgación de Información en Registro
**Vulnerabilidad:** El sistema revela si un usuario existe al validar el registro.

**Recomendación:** Implementar respuestas genéricas durante el registro. En caso de conflicto, retornar un mensaje como "El correo/usuario ya está registrado" sin especificar qué campo causó el conflicto. Además, utilizar registros detallados para monitoreo interno.

---

### 2. Mitigación de JWT con Secreto Débil
**Vulnerabilidad:** El secreto utilizado para firmar los JWT es débil y puede ser descifrado por fuerza bruta.

**Recomendación:** Utilizar una clave secreta suficientemente larga (mínimo 256 bits) y aleatoria. Considerar el uso de algoritmos más robustos como RS256 (RSA) en lugar de HS256 (HMAC). Generar secretos usando generadores criptográficos seguros y almacenarlos en variables de entorno protegidas.

---

### 3. Mitigación de Manipulación de Tokens JWT
**Vulnerabilidad:** Es posible falsificar tokens JWT válidos conociendo el secreto.

**Recomendación:** Aunque esta vulnerabilidad es consecuencia de la anterior (secreto débil), implementar validación adicional como: incluir un timestamp de emisión (iat) y verificar que no sea demasiado antiguo, implementar una lista negra (blacklist) de tokens revocados, usar algoritmos asimétricos (RS256), y validar el token en cada petición sensible.

---

### 4. Mitigación de Almacenamiento Inseguro en localStorage
**Vulnerabilidad:** Los tokens se almacenan en `localStorage` sin protección, siendo accesibles a scripts y ataques XSS.

**Recomendación:** Almacenar tokens en cookies **HttpOnly** y **Secure**, evitando que JavaScript pueda acceder a ellas. Si es necesario usar localStorage, implementar rotación de tokens cada 15-30 minutos, cifrar el contenido en cliente (aunque con ciertas limitaciones), y garantizar HTTPS obligatorio.

---

### 5. Mitigación de Escalada de Privilegios
**Vulnerabilidad:** Un usuario autenticado puede cambiar su rol elevando privilegios.

**Recomendación:** Implementar validación estricta de roles y permisos en el backend para cada endpoint sensible. Los roles nunca deben ser confíables desde el frontend. Mantener un registro de auditoría detallado de cambios de rol. Implementar verificación multifactor para operaciones administrativas críticas.

---

### 6. Mitigación de Cross-Site Scripting (XSS)
**Vulnerabilidad:** El campo de dirección permite la ejecución de código JavaScript.

**Recomendación:** Implementar sanitización de entrada: validar y filtrar caracteres especiales, usar librerías como DOMPurify. Codificar la salida en HTML entities al mostrar datos del usuario. Establecer una **Content Security Policy (CSP)** restricta que evite la ejecución de scripts inline. Realizar pruebas de XSS regularmente.

---

### 7. Mitigación de Inyección de Comandos
**Vulnerabilidad:** El endpoint DELETE es susceptible a inyección de comandos del sistema operativo.

**Recomendación:** Nunca construir comandos concatenando entrada del usuario. Utilizar funciones de API seguras que no invoquen un shell (ej: spawn en Node.js en lugar de exec). Implementar whitelist de valores permitidos para parámetros críticos. Ejecutar la aplicación con priviliegios mínimos (no como root). Implementar validación estricta y sanitización de entrada. Usar sandboxing si es posible.

---

**¡Somos root!**
