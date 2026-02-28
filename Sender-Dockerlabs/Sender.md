# Sender

## Resumen ejecutivo
El laboratorio **Sender** está diseñado para practicar técnicas de reconocimiento, explotación de credenciales débiles y desbordamiento de búfer en un servicio personalizado. El objetivo final es obtener acceso privilegiado (root) en la máquina víctima. Las dos vulnerabilidades principales abordadas son:

1. Credenciales obtenidas mediante enumeración y fuerza bruta basadas en contenido de la web.
2. Buffer overflow en un binario con privilegios de root que escucha en un puerto TCP.

El flujo del laboratorio recorre el uso de herramientas comunes (nmap, gobuster, hydra, cewl, gdb, metasploit) y culmina con la ejecución de un payload para ganar una shell root.

---

## 1. Reconocimiento inicial

Se inicia con un escaneo de **nmap**, identificando los puertos TCP 22 (SSH) y 80 (HTTP) abiertos:

![alt text](img/1-nmap.png)

Se intenta enumerar directorios con **gobuster**, sin encontrar rutas adicionales. A continuación se explora el servicio web.

![alt text](img/2-http.png)

## 2. Exploración del servicio HTTP

La página web muestra un tema de un dispositivo o aplicación para compartir archivos de forma rápida. Uno de los apartados ofrece la descarga de un binario llamado `sender`. La descripción sugiere que el ejecutable implementa un sencillo sistema TCP que se conecta a un puerto específico y envía información instantánea:

![alt text](img/3-sender.png)

## 3. Enumeración de credenciales

Se buscan vectores de ataque adicionales sin éxito. En la sección de comentarios se identifican tres usuarios potenciales. Se lanza un ataque de fuerza bruta por diccionario contra SSH usando *rockyou.txt*; no se obtiene ninguna contraseña válida. Se prueban variantes de nombres de usuario, también sin resultado.

Finalmente se usa la herramienta **cewl** para generar un diccionario de contraseñas basado en el contenido de una URL. Esto da una contraseña para el usuario `alex`:

![alt text](img/4-hydra.png)

Con estas credenciales se inicia sesión SSH:

![alt text](img/5-sshAlex.png)

## 4. Post-explotación en la cuenta de usuario

Dentro de la sesión como `alex`, se ejecuta `sudo -l` sin resultados; no tiene privilegios sudo. Se localiza la flag de usuario y un binario interesante llamado `server`.

![alt text](img/6-testAlex.png)

Se copia el binario a la máquina atacante para su análisis. Se observa que al ejecutarlo pone en escucha el puerto 7777 y llama a una función vulnerable para recibir datos. Un examen más detallado de la función muestra ausencia de comprobaciones de límites, lo que sugiere un posible desbordamiento de búfer.

![alt text](img/7-guidraServer.png)
![alt text](img/8-VulnFunctionServer.png)

## 5. Confirmación de vulnerabilidad

Al ejecutar `server` localmente y enviar datos excesivos se provoca un fallo de segmentación, confirmando la vulnerabilidad. El binario pertenece a `root`, por lo que es un vector de escalada de privilegios.

![alt text](img/9-TestServer.png)
![alt text](img/10-serverAnswer.png)

Se verifica además que **gdb** está instalado en la víctima, lo cual facilitará el desarrollo del exploit.

## 6. Cálculo del offset

Se genera un patrón con `pattern_create` de Metasploit (`-l 200`). Con el servidor ejecutándose dentro de gdb (`gdb ./server`) se envía el patrón y se observa dónde se produce el crash:

![alt text](img/11-offsetCreated.png)
![alt text](img/11-testOffset.png)

El marcador final revela la cantidad de bytes necesarios para alcanzar el retorno. Usando `pattern_offset` se determina que el desplazamiento (offset) óptimo es **76 bytes**.

![alt text](img/11-offset.png)

## 7. Preparación del entorno

Para simplificar el desarrollo del exploit, se desactiva la aleatorización del espacio de direcciones (`randomize_va_space`) estableciéndola a 0. Esto fija las direcciones en memoria y evita variaciones de ASLR.

![alt text](img/12-StaticMemory.png)

> **Nota:** Tras finalizar el laboratorio, se debe restaurar el valor a 2 para mantener las protecciones de seguridad.

Se inspecciona la memoria mediante `x/300wx $esp` en gdb para ver la ubicación donde comienza a cargarse el payload. Dado que la memoria es estática, se utiliza la dirección de inicio `0xffffd370` como referencia para la shellcode.

![alt text](img/12-MemoryPosition.png)

## 8. Explotación y escalada

Se desarrolla un script en Python que construye el payload y lo envía a través del binario `sender`, mediante llamadas al sistema (`os`). Al ejecutar el script desde la máquina atacante, se genera un desbordamiento que sobrescribe la memoria y permite ejecutar código arbitrario.

![alt text](img/13-RootShell.png)

El exploit resulta en una shell como **root**. Se obtiene la flag de la raíz y se completa el laboratorio.

![alt text](img/14-RootFlag.png)

## 9. Recomendaciones

1. **Contraseñas débiles y fuerza bruta:** Implementar políticas de contraseñas robustas, bloqueo de cuentas tras múltiples intentos fallidos y monitoreo de accesos. Además, usar autenticación de múltiples factores para SSH.
2. **Protección de binarios privilegiados:** Compilar con mecanismos de mitigación de desbordamientos (ASLR, stack canaries, PIE, NX) y revisar el código para validar correctamente los tamaños de buffer. Limitar el acceso a ejecutables de root.

---

*Fin del documento mejorado en español.*