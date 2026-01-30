# Inclusion — CTF (DockerLabs)

![Icono](Img/icon.png)

## Introducción

**Inclusion** es un laboratorio de tipo CTF de **DockerLabs** (dificultad media) diseñado para la demostración y análisis de una vulnerabilidad de inclusión local de archivos (**LFI — Local File Inclusion**). El objetivo del ejercicio es: identificar la vulnerabilidad, explotarla para obtener información sensible, y, con base en los hallazgos, proceder con la escalada de privilegios hasta obtener acceso root.

---

## Metodología

El proceso de validación y explotación siguió un flujo sistemático:

1. Reconocimiento de red y servicios (escaneo de puertos y detección de servicios).
2. Enumeración de recursos web (búsqueda de directorios y archivos con herramientas de fuerza bruta de rutas).
3. Detección y explotación de la vulnerabilidad LFI.
4. Extracción de información sensible (p. ej. `/etc/passwd`) y análisis de cuentas detectadas.
5. Ataques de fuerza bruta y transferencia de herramientas para pruebas internas.
6. Reconocimiento interno y verificación de vectores de escalada (privilegios `sudo`, archivos SUID, etc.).

---

## Hallazgos

### 1) Reconocimiento

Se inició con un escaneo de puertos que identificó los servicios **HTTP (puerto 80)** y **SSH (puerto 22)** como accesibles, lo que indica la presencia de una aplicación web y un servicio de acceso remoto.

![alt text](Img/nmap.png)

### 2) Enumeración web

La página HTTP por defecto fue accesible y, mediante `gobuster`, se descubrió el directorio `/shop`.

![alt text](Img/httpbasic.png)

![alt text](Img/gobusterScan.png)

Al acceder a `/shop` se observó un mensaje de error que incluía la referencia:

![alt text](Img/httpShop.png)

> "Error del sistema: ($_GET['archivo'])"

Ese patrón sugiere la existencia de una inclusión de archivo dependiente de un parámetro (LFI).

### 3) Explotación LFI

Al intentar incluir el archivo `/etc/passwd` a través del parámetro indicado, la aplicación devolvió el contenido del archivo, confirmando una LFI funcional.

![alt text](Img/LFIVulnerability.png)

> **Importante:** Toda información sensible mostrada en el laboratorio debe tratarse únicamente en un entorno controlado.

### 4) Ataque a credenciales

A partir del contenido de `/etc/passwd` se identificaron posibles cuentas (por ejemplo `seller`, `manchi`, `root`). Se ejecutó un ataque de fuerza bruta con `hydra` contra SSH que arrojó una contraseña válida para `manchi`.

![alt text](Img/manchiPasswd.png)

Con esas credenciales se estableció sesión SSH con el usuario `manchi`.

![alt text](Img/ManchiAcces.png)

### 5) Reconocimiento interno y escalada

En el interior del sistema `ls -la` no mostró archivos SUID evidentes. Se optó por realizar un ataque local de fuerza bruta a `su` usando un script y el diccionario `rockyou.txt`. El script y el diccionario fueron transferidos mediante `scp` y ejecutados localmente, lo cual reveló la contraseña de `seller`.

![alt text](Img/scpRockyou.png)
![alt text](Img/scpScript.png)
![alt text](Img/scriptExect.png)
![alt text](Img/SellerPasswd.png)

Al inspeccionar privilegios con `sudo -l` se detectó que la configuración permitía la ejecución de `php` sin requerir contraseña. Aprovechando este vector, se ejecutó:

```bash
sudo php -r "system('/bin/bash');"
```

La ejecución anterior permitió obtener una shell con privilegios de root.

![alt text](Img/root.png)

---

## Conclusión

La prueba demostró que la aplicación es vulnerable a LFI, lo cual permitió la exposición de información sensible (como `/etc/passwd`) y la identificación de cuentas potenciales. La combinación de credenciales recuperables y una regla de `sudo` permisiva (ejecución de `php` sin contraseña) posibilitó la escalada hasta root. El impacto es alto en entornos productivos y crítico si existen cuentas con privilegios administrativos.

---

## Mitigación y recomendaciones de corrección

1. Validación y saneamiento de entradas:
   - Restringir y normalizar parámetros que accepten rutas de archivos. Implementar listas blancas y eliminar referencias a rutas absolutas del sistema.
2. Evitar inclusión de archivos desde entradas del usuario:
   - Eliminar o desactivar la inclusión dinámica de archivos desde parámetros. Si es imprescindible, validar estrictamente y usar rutas seguras.
3. Revisión de configuración `sudo`:
   - Evitar reglas que permitan la ejecución de intérpretes (p. ej. `php`, `python`, `bash`) sin contraseña. Aplicar el principio de menor privilegio y limitar comandos permitidos por usuario.
4. Auditoría de credenciales y protección:
   - Revisar y rotar credenciales detectadas; deshabilitar cuentas innecesarias y aplicar autenticación multifactor si procede.
5. Registro y monitoreo:
   - Implementar logging detallado y alertas para accesos inusuales y errores que indiquen explotación.
6. Hardening y segmentación de servicios:
   - Reducir la superficie de ataque, minimizar servicios expuestos y aplicar controles de red para restringir el acceso de administración.
---

**Fin del informe — Inclusion (DockerLabs)**
