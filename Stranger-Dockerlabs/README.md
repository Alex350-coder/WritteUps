
# README — Stranger Lab

## Descripción / Description

- Español: Este repositorio contiene el laboratorio "Stranger", diseñado para practicar reconocimiento, enumeración de servicios, análisis de contenidos web y técnicas de escalada de privilegios en una máquina vulnerable.
- English: This repository contains the "Stranger" lab, intended for hands-on practice in reconnaissance, web enumeration, credential discovery and privilege escalation on a vulnerable target.

## Alcance / Scope

- Español: El objetivo es demostrar un flujo completo: descubrimiento de servicios, enumeración web, extracción de artefactos sensibles, obtención de credenciales y escalada de privilegios hasta root.
- English: The lab demonstrates a full attack chain: service discovery, web enumeration, sensitive artifact extraction, credential harvesting and privilege escalation to root.

## Estructura del repositorio / Repository structure

- `Stanger.md` — Informe detallado en español con pasos y capturas.
- `StangerEN.md` — Versión profesional en inglés del informe.
- `QuickStart.md` — Resumen rápido con comandos reproducibles (ES/EN).
- `img/` — Carpeta con capturas y artefactos visuales referenciados en los informes.

## Requisitos / Prerequisites

- Una máquina atacante (Kali, Parrot o similar) con las herramientas:

	- `nmap`
	- `gobuster` (o `dirb`)
	- `hydra` (o `medusa`) para fuerza bruta
	- `openssl` para operaciones con claves RSA
	- `ssh` client

- Acceso a wordlists apropiadas (ej. SecLists).
- Entorno de laboratorio controlado (no ejecutar ataques contra sistemas sin autorización).

## Preparación del entorno / Environment setup

1. Actualizar paquetes y herramientas (ejemplo para Debian/Ubuntu):

```bash
sudo apt update && sudo apt install -y nmap gobuster hydra openssl
```

2. Colocar wordlists en una ruta conocida (p.ej. `/usr/share/wordlists/`).

## Resumen del flujo de trabajo / Workflow summary

1. Reconocimiento: `nmap` para identificar puertos y servicios.
2. Enumeración web: `gobuster` para encontrar recursos ocultos.
3. Recuperación de artefactos: descarga de ficheros desde HTTP/FTP.
4. Cripto/descifrado: uso de la clave privada encontrada para descifrar `private.txt`.
5. Acceso inicial: probar credenciales en SSH.
6. Pivot y escalada: cambiar de usuario (`su`) y revisar `sudo -l` para obtener shell root.

## Comandos de referencia / Reference commands

- Escaneo básico con nmap:

```bash
nmap -sC -sV -oN nmap.txt <IP>
```

- Enumeración de directorios web con gobuster:

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 50
```

- Ataque por fuerza bruta FTP con hydra:

```bash
hydra -l admin -P /path/to/wordlist.txt ftp://<IP>
```

- Descarga de fichero vía FTP (ejemplo desde cliente ftp):

```text
ftp> get private.txt
```

- Descifrar RSA (ejemplo con openssl si el cifrado y formato son compatibles):

```bash
openssl rsautl -decrypt -inkey private_key.pem -in private.txt -out decrypted.txt
```

- Acceder por SSH y escalar:

```bash
ssh mwheeler@<IP>
su admin
sudo -l
sudo -i
```

> Nota: sustituir `<IP>` y rutas a wordlists por los valores correspondientes en tu entorno.

## Mitigación y buenas prácticas / Mitigation & Best Practices

- No exponer servicios inseguros: sustituir FTP por SFTP/FTPS o deshabilitarlo si no es necesario.
- Políticas de contraseñas y MFA: exigir contraseñas robustas y, cuando sea posible, autenticación multifactor.
- Proteg er claves privadas: almacenar en hardware seguro o vaults y aplicar permisos restrictivos (`chmod 600`).
- Limitación de intentos: implementar bloqueo temporal y alertas tras múltiples intentos fallidos.
- Auditoría de `sudo`: revisar y restringir privilegios, evitar configuraciones que permitan elevación indiscriminada.
- Registro y monitoreo: centralizar logs y usar detección de intrusiones para identificar patrones de reconocimiento o fuerza bruta.
- Gestión de contenido público: evitar incluir pistas o credenciales en contenidos accesibles públicamente; usar entornos de pruebas aislados.

