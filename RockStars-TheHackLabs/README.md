# Rock Stars — The Hacker Labs

## Descripción

Este repositorio documenta el laboratorio de hacking "Rock Stars" de The Hacker Labs. El objetivo del ejercicio es explotar una vulnerabilidad de inclusión local de archivos (LFI), obtener acceso inicial y escalar privilegios a través de configuraciones inseguras de `sudo` y manipulación del entorno Python.

This repository documents the "Rock Stars" hacking lab from The Hacker Labs. The exercise goal is to exploit a local file inclusion (LFI) vulnerability, achieve initial access, and escalate privileges through insecure `sudo` configuration and Python environment manipulation.

## Contenido

- `RockStars.md` — documentación detallada en español.
- `RockStarsEN.md` — documentación detallada en inglés.
- `QuickStart.md` — guía rápida bilingüe de los pasos críticos.

## Requisitos / Requirements

- Linux / Pentesting environment
- Herramientas: `nmap`, `gobuster`, `ffuf`, `curl`, `ssh`, `john`, `hydra`, `wget`, `python`
- Acceso a la máquina objetivo del laboratorio

## Cómo usar / How to use

1. Lee primero `QuickStart.md` para seguir los pasos críticos.
2. Consulta `RockStars.md` para obtener una descripción técnica completa en español.
3. Consulta `RockStarsEN.md` para la versión en inglés.

## Objetivos principales / Main objectives

- Identificar y explotar LFI.
- Obtener credenciales iniciales mediante exposición de archivos.
- Escalar privilegios mediante configuraciones de `sudo` mal restringidas.
- Abusar de la búsqueda de módulos Python para conseguir ejecución de código.
- Obtener acceso root y leer la flag final.

## Recomendaciones de seguridad / Security recommendations

- Valida y filtra correctamente la entrada del usuario.
- Evita exponer archivos de configuración sensibles en el directorio web.
- Restringe los permisos de `sudo` y elimina comandos innecesarios.
- Protege directorios y scripts con permisos adecuados.
- Audita los servicios y utilidades que se ejecutan con privilegios elevados.
