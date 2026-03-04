# Subversion Lab

## Descripción / Description

Este repositorio contiene el material de un laboratorio de hacking enfocado
en un servicio Subversion vulnerable. Se incluye el documento principal con el
análisis completo, la versión en inglés, y una guía rápida para completar el
laboratorio.

This repository holds the materials for a hacking lab centered on a vulnerable
Subversion service. It includes the main analysis document, an English version,
and a quick start guide.

## Requisitos / Requirements

- Máquina virtual o entorno con el servicio Subversion expuesto (según el lab).
- Herramientas de reconocimiento: nmap, gobuster.
- Wordlist (por ejemplo, rockyou.txt) y cliente `svn`.
- Python 3 para ejecutar los scripts de fuerza bruta y explotación.
- Ghidra u otra herramienta de ingeniería inversa.

Requirements mirror the tools needed to perform scans, brute-force the
repository, and analyze the binary.

## Documentación / Documentation

- `Subversion.md` – Documento principal en español con explicación detallada.
- `SubversionEn.md` – English translation of the full report.
- `QuickStart.md` – Steps summary for rapidly solving the lab.


## Uso / Usage

1. Lea `QuickStart.md` para un recorrido corto.
2. Consulte `Subversion.md` o `SubversionEn.md` para el análisis completo y el
   exploit.
3. Ejecute los scripts provistos desde el directorio correcto en su entorno de
test.

Repita los pasos en el orden indicado y consulte los archivos de imagen para
referencia visual.

## Licencia / License

El contenido es educativo y puede ser utilizado para propósitos de aprendizaje
únicamente.
