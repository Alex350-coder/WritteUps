# Sender - Laboratorio de Hacking

## Descripción / Description
**Español:**
Este laboratorio está diseñado para practicar técnicas de reconocimiento de red, fuerza bruta de credenciales, análisis binario y explotación de desbordamientos de búfer para escalar privilegios hasta root.

**English:**
This lab is designed to practice network reconnaissance, credential brute‑force, binary analysis, and buffer overflow exploitation to escalate privileges to root.

## Requisitos / Requirements
- Máquina objetivo con puertos 22 y 80 abiertos.
- Acceso a herramientas: `nmap`, `gobuster`, `hydra`, `cewl`, `gdb`, `metasploit` (o msfvenom), `python`.
- Conexión de red entre atacante y víctima.

## Documentación / Documentation
- **Guía detallada en español:** [Sender.md](Sender.md)
- **Detailed guide in English:** [Sender En.md](Sender%20En.md)
- **Quick start / Guía rápida:** [QuickStart.md](QuickStart.md)

## Flujo del laboratorio / Lab workflow
1. Reconocimiento con nmap y exploración web.
2. Generación de diccionarios y brute-force SSH.
3. Análisis del binario `server` y confirmación de vulnerabilidad.
4. Cálculo del offset y desactivación de ASLR.
5. Creación de exploit y obtención de shell root.

## Notas de seguridad / Security notes
- Restaurar `randomize_va_space` a 2 tras la práctica.
- Ejecute los ataques únicamente en entornos controlados y autorizados.

---

*Este README está diseñado para guiar al estudiante a través del laboratorio Sender. Consulte los archivos vinculados para instrucciones paso a paso y explicaciones técnicas.*