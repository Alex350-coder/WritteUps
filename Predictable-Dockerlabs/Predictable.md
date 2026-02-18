
Predictable

# Resumen

Este laboratorio guía al alumno en el análisis y explotación de un servicio HTTP vulnerable que expone una secuencia numérica generada por un Generador Congruencial Lineal (LCG). A partir de un escaneo inicial se identifican servicios accesibles, se revierte parcialmente el generador para predecir valores, se obtienen credenciales, se accede por SSH a un entorno restringido (pyjail), y finalmente se explota una configuración de `sudo` para obtener una shell con privilegios de root.

# Descubrimiento inicial

El laboratorio comienza con un escaneo de puertos usando Nmap. Se identifican dos servicios relevantes: SSH en el puerto 22 y un servicio HTTP en el puerto 1111.

![alt text](img/nmap.png)

# Análisis del servicio HTTP

Al acceder al servicio HTTP se muestra una interfaz que expone una serie de números que siguen un patrón predecible.

![alt text](img/http.png)

# Ingeniería inversa del generador LCG

El análisis del código fuente del servicio indica que la secuencia numérica está generada por un Generador Congruencial Lineal (LCG). 

![alt text](img/httpLCG.png)

Para determinar valores faltantes se intenta realizar ingeniería inversa de la fórmula del LCG. Dado el margen de error en algunos cálculos manuales, se utiliza un script de terceros (elaborado por Ghxstsec) que, al completar los parámetros requeridos, permite predecir el siguiente número en la secuencia.

![alt text](img/NextNumber.png)

# Obtención de credenciales y acceso SSH

Después de predecir el valor requerido, el servicio HTTP revela información que parece corresponder a credenciales. Estas credenciales se prueban contra el servicio SSH y permiten iniciar sesión, lo que deriva en un entorno restringido tipo "pyjail" (entorno Python con restricciones).

![alt text](img/sshCredentials.png)
![alt text](img/pyjail.png)

# Interacción con la pyjail y escalada local

Dentro de la pyjail el entorno presenta diversos mecanismos bloqueados que impiden la ejecución de operaciones fuera de la jaula:

![alt text](img/block.png)

Tras varios intentos (incluyendo ideas asistidas por IA), se identifica una función que permite obtener una shell funcional como el usuario `mash`.

![alt text](img/outPyjail.png)

# Enumeración de privilegios y análisis del binario `shell`

Al ejecutar `sudo -l` se detecta que el usuario puede ejecutar un script llamado `shell` ubicado en `/opt` sin contraseña, pero que requiere un parámetro para funcionar correctamente. Al ejecutar `shell -h` se descubre una pista adicional: `radare2` está instalado en el sistema.

![alt text](img/radare2.png)

Se procede a analizar el script `shell` con `radare2`, examinando principalmente el método `main` mediante `pdf @main`.

![alt text](img/r2Shell.png)

El análisis revela las condiciones necesarias para que el script abra una shell con privilegios de root:

- El primer bit no debe ser 1.
- El primer bit debe ser 0 obligatoriamente.
- Se requiere leer al menos 6 bits, y el último no debe ser x01.

# Explotación y obtención de root

Tras múltiples intentos infructuosos para cumplir exactamente las condiciones, se opta por una solución pragmática: crear en otra carpeta un archivo `shell` cuyo contenido satisfaga las condiciones (por ejemplo, seis valores en `0`) y, desde esa carpeta, ejecutar el script objetivo con `sudo /opt/shell 0`. Esta ejecución provoca que el script otorgue una shell con privilegios de root.

![alt text](img/root.png)

# Recomendaciones para mitigar y prevenir estas vulnerabilidades

Estas recomendaciones están orientadas a reducir la superficie de ataque y prevenir vectores similares a los explotados en el laboratorio:

- **Asegurar servicios expuestos:** Evitar exponer servicios innecesarios al exterior. Limitar el acceso a SSH por IP/VPN y usar autenticación por claves y políticas de contraseñas robustas.
- **Validación de entradas y parámetros:** Los scripts ejecutables vía `sudo` deben validar estrictamente sus parámetros y no asumir formatos inseguros. Evitar ejecutar scripts ubicados en directorios que puedan ser manipulados por usuarios no privilegiados.
- **Restringir reglas de sudo:** Minimizar las entradas en `/etc/sudoers`. No permitir la ejecución sin contraseña de binarios o scripts sensibles; en caso necesario, restringir la ejecución a comandos con ruta absoluta y parámetros específicos validados.
- **Harden de entornos sandbox (pyjail):** Implementar controles adicionales en las jaulas (por ejemplo, seccomp, namespaces, límites estrictos de permisos y path restrictions) y auditar los puntos de escape posibles.
- **Usar generadores criptográficamente seguros:** No confiar en generadores determinísticos simples (como LCG) para valores que requieran imprevisibilidad. Emplear CSPRNG para tokens, claves y otros valores sensibles.
- **Registro y monitorización:** Habilitar logging detallado y alertas para actividades inusuales (escaneos, autenticaciones fallidas, ejecuciones de sudo). Mantener registros centralizados para análisis forense.
- **Revisión de código y pruebas de seguridad:** Revisar scripts y servicios antes de su despliegue; realizar pruebas de caja negra/caixa blanca y análisis estático para detectar patrones inseguros.
- **Principio de mínimo privilegio:** Ejecutar servicios con cuentas dedicadas y privilegios mínimos; evitar su ejecución como root cuando no sea estrictamente necesario.

---

