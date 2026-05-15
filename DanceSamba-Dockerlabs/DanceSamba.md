# Dance Samba

**Nivel**: Medio  
**Laboratorio**: Dockerlabs

## Resumen Ejecutivo

Dance Samba es un laboratorio de penetración de nivel medio que demuestra cómo múltiples vulnerabilidades de configuración, cuando se encadenan correctamente, pueden comprometer completamente un sistema Linux. A través de una combinación de acceso anónimo a FTP, configuración deficiente de SMB, almacenamiento inseguro de credenciales y permisos sudo excesivos, es posible escalar privilegios desde un usuario no privilegiado hasta obtener acceso root.

## Fase 1: Reconocimiento y Enumeración

### 1.1 Escaneo de Puertos

El laboratorio comienza con un escaneo de nmap que revela los siguientes servicios activos:

| Puerto | Protocolo | Servicio | Versión |
|--------|-----------|----------|---------|
| 21/TCP | FTP | vsftpd | 3.0.5 |
| 22/TCP | SSH | OpenSSH | 9.6p1 Ubuntu |
| 139/TCP | NetBIOS-SSN | Samba smbd | 4 |
| 445/TCP | NetBIOS-SSN | Samba smbd | 4 |

![alt text](img/1-nmap.png)

### 1.2 Identificación del Vector de Ataque

Dado que no hay servicios web accesibles, se dirige la atención al servicio FTP. Sin credenciales obvias disponibles, se intenta acceder con usuario `anonymous` y contraseña vacía, lo que tiene éxito:

![alt text](img/2-FtpAnonymous.png)

De manera paralela, se enumeran los servicios SMB, revelando la existencia de un recurso compartido personal: `macarena`.

![alt text](img/3-Macarena.png)

## Fase 2: Extracción de Credenciales

### 2.1 Descubrimiento de Pistas en FTP

Dentro del servicio FTP, se descubre una nota que se descarga a la máquina local:

![alt text](img/4-Nota.png)

Esta nota proporciona pistas críticas sobre posibles credenciales de acceso al recurso compartido SMB de macarena.

### 2.2 Acceso al Recurso SMB

Utilizando las pistas obtenidas, se inicia sesión en el recurso compartido SMB:

![alt text](img/5-SMBMacarena.png)

El acceso es exitoso. Se descarga el archivo `user.txt`, que revela la primera bandera:

![alt text](img/5-FristFlag.png)

## Fase 3: Evasión de Autenticación SSH

### 3.1 Técnica de Inyección de Clave SSH

Dado que no existen otras vías aparentes de avance, se implementa una técnica avanzada de evasión: se genera un par de claves RSA localmente y se suben al directorio `authorized_keys` en `.ssh` dentro del recurso SMB. Esta técnica funciona porque SSH confía en las claves presentes en este directorio, independientemente de su origen, siempre que la configuración de SMB sea deficiente:

```bash
ssh-keygen -f id_rsa -P x -q
mv id_rsa.pub authorized_keys
```

**Nota**: Es necesario crear el directorio `.ssh` en el SMB y usar `put` para subir el archivo `authorized_keys`. Esta técnica solo funciona si el recurso SMB está mal configurado y permite escritura en directorios críticos.

![alt text](img/6-RSA.png)

### 3.2 Acceso SSH Exitoso

Posteriormente, se logra acceder por SSH sin proporcionar contraseña, ya que la clave inyectada funciona correctamente:

![alt text](img/7-SSHmacarena.png)

## Fase 4: Enumeración del Sistema Local

### 4.1 Búsqueda de Usuarios Adicionales

Se examina el archivo `/etc/passwd` en busca de otros usuarios potenciales. Los resultados muestran que solo existen dos usuarios: `root` y `macarena`.

![alt text](img/8-CatETCPasswd.png)

### 4.2 Búsqueda de Binarios SUID

Se realiza una búsqueda de binarios con el bit SUID establecido usando `find`, pero no se encuentra nada explotable:

![alt text](img/8-find.png)

### 4.3 Descubrimiento de Credencial Oculta

Durante la exploración del sistema de archivos, se descubre un directorio denominado `secret` que contiene un archivo hash:

![alt text](img/9-hash.png)

## Fase 5: Decodificación de Credencial

### 5.1 Decodificación Múltiple

Se utiliza un comando que encadena dos decodificaciones para revelar la credencial oculta:

```bash
echo "MMZVM522LBFHWXSJYYWG3KW05MVQTT2MQZDS6K2IE6T2==" | base32 -d | base64 -d
```

El primer intento con `base32 -d` produce otro hash; sin embargo, este hash está codificado en base64, por lo que se aplica otra decodificación para obtener la credencial final:

![alt text](img/10-hashDecrypt.png)

## Fase 6: Escalada de Privilegios

### 6.1 Verificación de Permisos Sudo

Usando la credencial decodificada para el usuario `macarena` (recuerde que el acceso SSH fue mediante inyección de clave, no mediante contraseña), se ejecuta `sudo -l` y se proporciona la contraseña. La salida revela una configuración peligrosa del sudoers: el usuario puede ejecutar el comando `file` sin contraseña como root:

![alt text](img/11-sudo-L.png)

### 6.2 Lectura de Archivo Protegido

Se realiza una exploración del sistema y se identifica un archivo crítico en `/opt/password.txt` que solo puede ser leído por root:

![alt text](img/11-password.png)

Utilizando el privilegio sudo sobre `file`, se puede leer este archivo protegido mediante:

```bash
sudo file -f /opt/password.txt
```

**Nota**: Se probaron varias variantes del comando antes de llegar a esta sintaxis exitosa.

![alt text](img/12-fileToRead.png)

### 6.3 Acceso Root Exitoso

La credencial revelada corresponde al usuario `root`. Se autentica exitosamente con estas credenciales, obteniendo acceso administrativo completo al sistema:

![alt text](img/root.png)

## Análisis de Vulnerabilidades

| # | Vulnerabilidad | Descripción | CVSS |
|---|---|---|---|
| 1 | Acceso Anónimo FTP | El servicio FTP permite acceso sin credenciales | 7.5 |
| 2 | Configuración Deficiente de SMB | Permite escritura en directorios críticos como `.ssh` | 8.8 |
| 3 | Almacenamiento Inseguro de Credenciales | Credenciales codificadas en el sistema de archivos | 9.1 |
| 4 | Configuración Excesiva de Sudoers | Binario `file` ejecutable como root sin validación | 8.4 |

## Recomendaciones de Mitigación

### 1. Deshabilitar Acceso Anónimo a FTP
**Vulnerabilidad**: Acceso anónimo al servicio FTP  
**Recomendación**: Deshabilitar la autenticación anónima en vsftpd editando `/etc/vsftpd.conf` y estableciendo `anonymous_enable=NO`. Requiere credenciales válidas para acceder a cualquier recurso FTP.

### 2. Asegurar Configuración de Permisos SMB
**Vulnerabilidad**: SMB mal configurado permitiendo escritura en directorios críticos  
**Recomendación**: Implementar permisos restrictivos en la configuración de Samba (`/etc/samba/smb.conf`). Establecer `writable=no` para recursos que no requieren escritura, configurar ACLs adecuadas, y evitar mapeo de directorios sensibles del sistema como `.ssh`.

### 3. Implementar Gestión Segura de Credenciales
**Vulnerabilidad**: Credenciales almacenadas en texto claro y codificadas inseguramente en el sistema de archivos  
**Recomendación**: Utilizar un gestor de secretos (ej: HashiCorp Vault, AWS Secrets Manager) para almacenar credenciales. Nunca almacenar secretos en el disco en texto claro. Implementar rotación de credenciales periódica y auditoría de acceso.

### 4. Restringir Configuración de Sudoers
**Vulnerabilidad**: Permisos excesivos en sudoers permitiendo ejecución de binarios complejos como root  
**Recomendación**: Revisar y restringir la configuración sudoers del archivo `/etc/sudoers` usando `visudo`. Especificar únicamente los comandos específicos y argumentos necesarios para cada usuario. Implementar logging de sudoers, considerar usar `sudo` sin NOPASSWD para operaciones sensibles, e implementar MFA para operaciones críticas.

---

**Fecha de creación**: 2026  
**Tipo de laboratorio**: Pentesting - Encadenamiento de vulnerabilidades  
**Dificultad**: Media


