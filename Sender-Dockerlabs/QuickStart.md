# QuickStart / Guía Rápida

## Español
1. Escanea la máquina con `nmap -sC -sV -p 22,80 <IP>`.
2. Visita el servicio HTTP y descarga el binario `sender`.
3. Usa `cewl` para generar un diccionario desde la web y ejecuta `hydra -l alex -P wordlist.txt ssh://<IP>`.
4. Inicia sesión SSH como `alex` y obtiene la flag de usuario.
5. Copia el binario `server` y examínalo: escucha puerto 7777 y tiene una función vulnerable.
6. En la víctima, ejecuta `gdb ./server`, envía un patrón de 200 bytes y calcula el offset (76).
7. Desactiva ASLR (`echo 0 > /proc/sys/kernel/randomize_va_space`) y toma nota de la dirección de carga (`0xffffd370`).
8. Crea un exploit (ej. script Python) con 76 bytes de relleno más la dirección de shellcode y envíalo usando el binario `sender`.
9. Obtén una shell root y lee `/root/root.txt`.

## English
1. Scan the target with `nmap -sC -sV -p 22,80 <IP>`.
2. Browse the HTTP service and download the `sender` binary.
3. Use `cewl` to build a wordlist from the web page and run `hydra -l alex -P wordlist.txt ssh://<IP>`.
4. SSH in as `alex` and capture the user flag.
5. Transfer the `server` binary and analyze it: it listens on port 7777 and contains a vulnerable function.
6. On the victim, run `gdb ./server`, send a 200‑byte pattern and calculate the offset (76).
7. Disable ASLR (`echo 0 > /proc/sys/kernel/randomize_va_space`) and note the load address (`0xffffd370`).
8. Craft an exploit (e.g. Python script) with 76 bytes of padding plus the shellcode address and send it via `sender`.
9. Acquire a root shell and read `/root/root.txt`.
