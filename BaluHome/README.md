# BaluHome - Hacking Laboratory Writeup / Reporte de Laboratorio BaluHome

Este repositorio contiene la documentación detallada y guías de explotación para el laboratorio **BaluHome** de la plataforma **Dockerlabs** (Dificultad: Medio).
This repository contains detailed documentation and exploitation walkthroughs for the **BaluHome** lab from the **Dockerlabs** platform (Difficulty: Medium).

---

## 🇪🇸 Español

### Descripción del Laboratorio
**BaluHome** es un entorno controlado de pruebas de penetración y hacking ético que simula un clon de YouTube. El objetivo de este laboratorio es explotar una cadena de vulnerabilidades web comunes y configuraciones de sistema inseguras para lograr el compromiso total del sistema (acceso como `root`).

Las fases cubiertas en el writeup son:
1. **Reconocimiento y Enumeración**: Escaneo de servicios con `nmap`.
2. **Acceso Inicial**: Inyección de Scripts de Sitios Cruzados de tipo Almacenado (Stored XSS) en la sección de subtítulos para robar cookies de sesión de administrador.
3. **Ejecución Remota de Código (RCE)**: Evasión de validación en subida de archivos (MIME Type Bypass) en el panel administrativo para subir y ejecutar un archivo de comandos (reverse shell).
4. **Movimiento Lateral**: Fuerza bruta sobre el usuario local `balutin` utilizando scripts automatizados y diccionarios como `rockyou.txt`.
5. **Escalada de Privilegios**: Abuso de un script de copia de seguridad con permisos de escritura laxos para el grupo `mantenimiento` ejecutado por el usuario `root`.

### Requisitos del Sistema
Para reproducir este laboratorio, se recomienda contar con los siguientes elementos:
* Una distribución orientada a pruebas de penetración (ej., Kali Linux, Parrot OS).
* **Docker** instalado y configurado.
* El archivo de la máquina descargado desde [Dockerlabs](https://dockerlabs.es/).
* Herramientas de pruebas ofensivas:
  * **Nmap** (escaneo y reconocimiento).
  * **Burp Suite** (interceptación y manipulación de peticiones HTTP).
  * **Netcat** (establecimiento de reverse shell e intercambio de archivos).
  * Python 3 (servidor HTTP temporal).

### Enlaces de Documentación
* [Guía de Explotación Detallada (Español)](BaluHome.md)
* [Guía de Explotación Detallada (Inglés)](BaluHome-En.md)
* [Guía de Inicio Rápido / Comandos Clave (Bilingüe)](QuickStart.md)

---

## 🇬🇧 English

### Lab Description
**BaluHome** is a controlled penetration testing and ethical hacking environment simulating a YouTube replica. The main goal of this laboratory is to exploit a chain of common web vulnerabilities and insecure system configurations to gain full system compromise (root access).

The phases covered in this writeup include:
1. **Reconnaissance and Enumeration**: Port scanning and service discovery using `nmap`.
2. **Initial Access**: Stored Cross-Site Scripting (Stored XSS) in the subtitles field to hijack the administrator's session cookie.
3. **Remote Code Execution (RCE)**: File upload restriction bypass (MIME Type bypass) in the administrator panel to upload and execute a malicious shell script.
4. **Lateral Movement**: Local password brute-forcing against the `balutin` account using automation scripts and `rockyou.txt`.
5. **Privilege Escalation**: Exploitation of a system backup script with weak permissions writable by the `mantenimiento` group and executed by the `root` user.

### System Requirements
To replicate this laboratory environment, the following are recommended:
* A penetration testing operating system (e.g., Kali Linux, Parrot OS).
* **Docker** installed and configured.
* The lab environment package downloaded from [Dockerlabs](https://dockerlabs.es/).
* Offensive security tools:
  * **Nmap** (reconnaissance).
  * **Burp Suite** (HTTP interception and request manipulation).
  * **Netcat** (reverse shell handling and file transfer).
  * Python 3 (utility HTTP server).

### Documentation Links
* [Detailed Exploitation Guide (English)](BaluHome-En.md)
* [Detailed Exploitation Guide (Spanish)](BaluHome.md)
* [QuickStart Guide / Essential Commands (Bilingual)](QuickStart.md)
