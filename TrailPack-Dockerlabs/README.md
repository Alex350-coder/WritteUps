# Trail Pack - Advanced Web Security Laboratory

## Español

### Descripción

Trail Pack es un laboratorio avanzado de seguridad web diseñado para profesionales de ciberseguridad, estudiantes de seguridad informática y entusiastas del hacking ético. Este laboratorio simula un escenario realista de prueba de penetración donde los participantes deben identificar y explotar múltiples vulnerabilidades de seguridad críticas en una aplicación web.

### Objetivos de Aprendizaje

A través de la explotación de las vulnerabilidades en este laboratorio, aprenderás:

- **Bypass de Autenticación Multifactor (MFA):** Identificar y explotar mecanismos débiles de rate limiting basados en IP
- **Inyección de Comandos:** Detectar y explotar vulnerabilidades de command injection para obtener acceso al sistema
- **Escalada de Privilegios:** Explotar permisos SUID mal configurados para obtener acceso root
- **Análisis de Código Fuente:** Identificar vulnerabilidades mediante análisis estático de aplicaciones web
- **Manipulación de Sesiones:** Explotar mecanismos débiles de encriptación de cookies y tokens
- **Análisis de Seguridad:** Utilizar herramientas como Burpsuite para interceptar y modificar solicitudes HTTP

### Nivel de Dificultad

**Intermedio (Intermediate)**

Se recomienda tener conocimiento previo en:
- Conceptos básicos de HTTP y arquitectura web
- Manejo de terminal/línea de comandos
- Conocimiento fundamental de seguridad web (OWASP Top 10)

### Requisitos

#### Hardware

- Máquina atacante (Kali Linux o distribución similar)
- 2GB de RAM mínimo
- 5GB de espacio disponible en disco

#### Software y Herramientas

**Herramientas Recomendadas:**
- `nmap` - Escaneo de puertos
- `gobuster` - Enumeración de directorios
- `burpsuite` - Interceptación y modificación de tráfico HTTP
- `netcat` (nc) - Listener para reverse shell
- `Python 3` - Para scripts de automatización

**Instalación:**
```bash
# En Kali Linux
sudo apt update
sudo apt install nmap gobuster netcat-openbsd python3
# Burpsuite debe descargarse desde https://portswigger.net/burp/communitydownload
```

#### Stack de la Aplicación

La aplicación web utiliza:
- Python/Flask - Backend
- HTML/CSS/JavaScript - Frontend
- Linux - Sistema operativo (Dockerlab)

### Estructura del Laboratorio

El laboratorio está dividido en 6 fases principales:

1. **Fase 1: Reconocimiento** - Escaneo y enumeración de servicios
2. **Fase 2: Descubrimiento de Funcionalidades** - Mapeo de la aplicación
3. **Fase 3: Bypass de MFA** - Explotación de mecanismo de autenticación débil
4. **Fase 4: Command Injection** - Ejecución de comandos arbitrarios
5. **Fase 5: Escalada de Privilegios** - Obtención de acceso root
6. **Fase 6: Privacidad y Exfiltración** - Obtención de la flag

### Vulnerabilidades Cubiertas

| Vulnerabilidad | CWE | CVSS |
|---|---|---|
| Weak MFA Implementation | CWE-287 | 7.5 |
| Command Injection | CWE-78 | 9.8 |
| Insecure Cookie Handling | CWE-614 | 7.5 |
| Improper SUID Configuration | CWE-269 | 8.8 |

### Documentación

- **[TrailPack.md](TrailPack.md)** - Writeup detallado en español con explicaciones paso a paso
- **[TrailPackEN.md](TrailPackEN.md)** - Writeup detallado en inglés
- **[QuickStart.md](QuickStart.md)** - Guía rápida con pasos críticos para resolver el laboratorio

3. Consultar [QuickStart.md](QuickStart.md) para los pasos de explotación

Para un análisis detallado, consultar [TrailPack.md](TrailPack.md)

### Recomendaciones de Seguridad

El laboratorio incluye recomendaciones específicas para mitigar cada una de las vulnerabilidades explotadas. Estas se encuentran en la sección final del documento de writeup.

**Puntos clave:**
- Implementación robusta de MFA
- Validación y sanitización de entrada
- Protección criptográfica de sesiones
- Minimización de permisos SUID

### Licencia

Este material educativo es proporcionado con propósitos de aprendizaje y formación en seguridad ética.

---

## English

### Description

Trail Pack is an advanced web security laboratory designed for cybersecurity professionals, information security students, and ethical hacking enthusiasts. This laboratory simulates a realistic penetration testing scenario where participants must identify and exploit multiple critical security vulnerabilities in a web application.

### Learning Objectives

Through exploiting vulnerabilities in this laboratory, you will learn:

- **Multi-Factor Authentication (MFA) Bypass:** Identify and exploit weak IP-based rate limiting mechanisms
- **Command Injection:** Detect and exploit command injection vulnerabilities to gain system access
- **Privilege Escalation:** Exploit misconfigured SUID permissions to obtain root access
- **Source Code Analysis:** Identify vulnerabilities through static analysis of web applications
- **Session Manipulation:** Exploit weak cookie and token encryption mechanisms
- **Security Analysis:** Use tools like Burpsuite to intercept and modify HTTP requests

### Difficulty Level

**Intermediate**

Prior knowledge recommended in:
- Basic concepts of HTTP and web architecture
- Terminal/command-line proficiency
- Fundamental web security knowledge (OWASP Top 10)

### Requirements

#### Hardware

- Attacker machine (Kali Linux or similar distribution)
- 2GB RAM minimum
- 5GB available disk space

#### Software and Tools

**Recommended Tools:**
- `nmap` - Port scanning
- `gobuster` - Directory enumeration
- `burpsuite` - HTTP traffic interception and modification
- `netcat` (nc) - Reverse shell listener
- `Python 3` - For automation scripts

**Installation:**
```bash
# On Kali Linux
sudo apt update
sudo apt install nmap gobuster netcat-openbsd python3
# Burpsuite must be downloaded from https://portswigger.net/burp/communitydownload
```

#### Application Stack

The web application uses:
- Python/Flask - Backend
- HTML/CSS/JavaScript - Frontend
- Linux - Operating system (Dockerlab)

### Laboratory Structure

The laboratory is divided into 6 main phases:

1. **Phase 1: Reconnaissance** - Service scanning and enumeration
2. **Phase 2: Functionality Discovery** - Application mapping
3. **Phase 3: MFA Bypass** - Exploitation of weak authentication mechanism
4. **Phase 4: Command Injection** - Arbitrary command execution
5. **Phase 5: Privilege Escalation** - Obtaining root access
6. **Phase 6: Privacy and Exfiltration** - Flag retrieval

### Vulnerabilities Covered

| Vulnerability | CWE | CVSS |
|---|---|---|
| Weak MFA Implementation | CWE-287 | 7.5 |
| Command Injection | CWE-78 | 9.8 |
| Insecure Cookie Handling | CWE-614 | 7.5 |
| Improper SUID Configuration | CWE-269 | 8.8 |

### Documentation

- **[TrailPack.md](TrailPack.md)** - Detailed writeup in Spanish with step-by-step explanations
- **[TrailPackEN.md](TrailPackEN.md)** - Detailed writeup in English
- **[QuickStart.md](QuickStart.md)** - Quick guide with critical steps to solve the laboratory

3. Refer to [QuickStart.md](QuickStart.md) for exploitation steps

For detailed analysis, refer to [TrailPack.md](TrailPack.md)

### Security Recommendations

The laboratory includes specific recommendations to mitigate each of the exploited vulnerabilities. These can be found in the final section of the writeup document.

**Key points:**
- Robust MFA implementation
- Input validation and sanitization
- Cryptographic protection of sessions
- Minimization of SUID permissions

### License

This educational material is provided for learning and ethical security training purposes.
