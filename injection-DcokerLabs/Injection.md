# Injection 🔐

**Injection** es un laboratorio bastante simple de **DockerLabs** que, sin embargo, trata un tema muy recurrente e importante en la seguridad informática: las conocidas **inyecciones SQL** o **SQLi**.

---

## 🔍 Reconocimiento Inicial

El laboratorio comienza, como es costumbre, con un escaneo de puertos usando **Nmap**. El escaneo se realiza con el parámetro `T4` para agilizar el proceso. 

![alt text](img/NmapScan.png)

### Puertos Descubiertos

Se encuentran dos puertos abiertos:
- **Puerto 22**: SSH
- **Puerto 80**: HTTP

### Análisis del Servicio HTTP

Se revisa el contenido del servicio HTTP, que es un formulario de inicio de sesión con dos campos de entrada:
- Nombre de usuario
- Contraseña

No se visualizan elementos adicionales. 

![alt text](img/httpLogIn.png)

### Búsqueda de Directorios

Siguiendo el protocolo usual, se realiza una búsqueda con **Gobuster**; sin embargo, no encuentra nada excepto un archivo `config.php` vacío, probablemente un error o documento de una idea inicial que no se implementó.

![alt text](img/Gosbuter.png)

## 💉 Explotación SQL Injection

Después de esto, y considerando que no hay otras vías, se decide tomar en cuenta el nombre del laboratorio y realizar una **inyección SQL**. Hay varias formas de hacerlo—ya sea mediante entrada directa en el formulario—pero se decide usar **Burp Suite** para examinar la estructura de la consulta que se realiza.

![alt text](img/NormalSQLQuerryBurp.png)

### Análisis de la Consulta

Se puede visualizar una consulta SQL muy básica, que podría haberse resuelto directamente, pero se continúa en este entorno. Para proceder con la **SQLi**, se realizan los cambios correspondientes. 

![alt text](img/SQLiBurp.png)

De esta forma se obtiene acceso y se muestra lo siguiente.

![alt text](img/SQLiSucces.png)

### Obtención de Credenciales

✅ **¡Bingo!** Se obtienen las credenciales y el nombre de usuario. Se decide utilizarlos en el servicio **SSH** del puerto 22. 

![alt text](img/sshDylan.png)

Se obtiene acceso.

⚠️ **Nota importante**: En el servicio HTTP se visualiza el usuario como `Dylan` con mayúscula; sin embargo, en SSH es `dylan` en minúscula. Como SSH es sensible a mayúsculas, esto genera fallos y posibles confusiones. 

## ⬆️ Escalada de Privilegios

Después de esto, se revisan varias opciones sin resultado, por lo que se opta por usar el comando `find` en una búsqueda de binarios que permitan realizar una escalada de privilegios.

![alt text](img/FindUserDylan.png)

### Binario Vulnerable

Y con esto se da en el clavo, ya que se observa que el binario `env` tiene permisos **SUID** (set user ID) y pertenece a `root`. Esto significa que se puede ejecutar con privilegios de root y, como el binario `env` permite la ejecución de una shell, el desafío está resuelto.

> 📝 **Nótese** que se utiliza la bandera `-p` para mantener los privilegios.

El binario se ejecuta de la siguiente manera: 

![alt text](img/envVulnerableBin.png)

### Resultado Final

🎉 **¡Finalmente, se obtiene acceso root!**

---

## 📊 Resumen del Laboratorio

| Paso | Descripción | Herramienta |
|------|-------------|-------------|
| 1️⃣ | Escaneo de puertos | Nmap |
| 2️⃣ | Búsqueda de directorios | Gobuster |
| 3️⃣ | Inyección SQL | Burp Suite |
| 4️⃣ | Acceso SSH | SSH Client |
| 5️⃣ | Escalada de privilegios | find + env |