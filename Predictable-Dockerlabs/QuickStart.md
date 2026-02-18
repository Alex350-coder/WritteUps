# QuickStart / Inicio rápido

ES / Español:

1. Escanea el objetivo para identificar servicios.

```bash
nmap -sC -sV -p22,1111 <IP>
```

2. Accede al servicio HTTP en el puerto 1111 y analiza la secuencia numérica.

3. Usa el script de Ghxstsec (o tu propio script LCG) para predecir el número siguiente y recuperar la información que revela credenciales.

4. Con las credenciales, conéctate por SSH:

```bash
ssh user@<IP> -p 22
```

5. Interactúa con la `pyjail` para identificar funciones explotables; consigue una shell como `mash`.

6. Ejecuta `sudo -l` y detecta la entrada que permite ejecutar `/opt/shell` sin contraseña.

7. Analiza `/opt/shell` con `radare2` o strumenti similares para conocer las condiciones requeridas.

```bash
radare2 -A /opt/shell
r2 /opt/shell
pdf @main
```

8. Solución práctica: crea un archivo `shell` en un directorio controlado con contenido que cumpla las condiciones (por ejemplo, seis ceros) y lanza:

```bash
sudo /opt/shell 0
```

9. Si todo es correcto, obtendrás una shell con privilegios de root.

---

EN / English:

1. Scan the target to identify services.

```bash
nmap -sC -sV -p22,1111 <IP>
```

2. Open the HTTP service on port 1111 and inspect the exposed numeric sequence.

3. Use the Ghxstsec script (or your own LCG tool) to predict the next number and obtain the credentials disclosed by the web service.

4. SSH into the box with the recovered credentials:

```bash
ssh user@<IP> -p 22
```

5. Interact with the `pyjail` to find a function that yields a shell as `mash`.

6. Run `sudo -l` to discover a passwordless entry for `/opt/shell`.

7. Analyze `/opt/shell` with `radare2` to determine required input conditions.

```bash
radare2 -A /opt/shell
pdf @main
```

8. Practical exploit: create a `shell` file in a writable directory with values satisfying the constraints (e.g., six zeros) and execute:

```bash
sudo /opt/shell 0
```

9. A successful execution will grant a root shell.

