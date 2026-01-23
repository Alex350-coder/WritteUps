# 📚 WhereIsMyWebShell - Dockerlabs Writeup

<div align="center">

![WhereIsMyWebShell](Img/icon.png)

**A comprehensive writeup for the WhereIsMyWebShell Dockerlabs challenge covering fuzzing, RCE, and reverse shells.**

[🇪🇸 Español](#versión-en-español) | [🇬🇧 English](#english-version)

</div>

---

## 📋 English Version

### Overview
This writeup provides a detailed walkthrough of the **WhereIsMyWebShell** Dockerlabs challenge, demonstrating techniques for identifying and exploiting web application vulnerabilities, specifically Remote Code Execution (RCE) and reverse shell exploitation.

### 🎯 Objectives
- Conduct port scanning and service enumeration
- Identify hidden web resources through directory fuzzing
- Discover vulnerable parameters through parametric fuzzing
- Exploit RCE vulnerability to establish command execution
- Establish a reverse shell for enhanced system access
- Locate hidden credentials and escalate privileges

### 📂 Contents

| File | Description |
|------|-------------|
| **[WhereIsMyWebShell-En.md](WhereIsMyWebShell-En.md)** | Complete professional writeup in English with detailed explanations and security recommendations |
| **[QuickStart.md](QuickStart.md)** | Quick resolution guide for rapid laboratory completion |
| **[WhereIsMyWebShell.md](WhereIsMyWebShell.md)** | Complete professional writeup in Spanish with detailed explanations and security recommendations |

### 🛠️ Tools & Technologies
- **Nmap**: Network port scanning
- **Gobuster**: Directory fuzzing
- **FFuz**: Parameter fuzzing
- **Netcat (nc)**: Reverse shell listener
- **Bash**: Shell scripting

### 📊 Key Vulnerabilities Covered
1. **Remote Code Execution (RCE)**: Unsafe command execution through web parameters
2. **Insufficient Input Validation**: Lack of parameter sanitization
3. **Insecure File Handling**: Exposure of sensitive files with weak permissions
4. **Missing Access Controls**: Unrestricted access to sensitive functionalities

### 🔍 Attack Phases
1. **Reconnaissance**: Port scanning and service identification
2. **Enumeration**: Directory and parameter discovery through fuzzing
3. **Exploitation**: RCE via vulnerable parameter
4. **Escalation**: Reverse shell establishment for privilege elevation
5. **Post-Exploitation**: Credential extraction and privilege escalation

### 🛡️ Security Recommendations
This writeup includes 5 comprehensive security recommendations to prevent RCE and reverse shell attacks:

1. Input Validation and Sanitization
2. Prohibition of Dangerous Functions
3. Access Control and Authentication
4. Principle of Least Privilege
5. Web Application Firewall (WAF) Implementation

### 💡 Learning Outcomes
Upon completing this challenge, you will understand:
- How web applications can be compromised through parameter manipulation
- The importance of input validation in web security
- How reverse shells work and their implications
- Privilege escalation techniques in Linux environments
- Defensive measures against common web exploitation attacks

---

## 📋 Versión en Español

### Descripción General
Este writeup proporciona una guía detallada del reto **WhereIsMyWebShell** de Dockerlabs, demostrando técnicas para identificar y explotar vulnerabilidades en aplicaciones web, específicamente Ejecución Remota de Comandos (RCE) y explotación de shells inversas.

### 🎯 Objetivos
- Realizar escaneo de puertos y enumeración de servicios
- Identificar recursos web ocultos mediante fuzzing de directorios
- Descubrir parámetros vulnerables mediante fuzzing paramétrico
- Explotar vulnerabilidades RCE para establecer ejecución de comandos
- Establecer una shell inversa para acceso mejorado al sistema
- Localizar credenciales ocultas y escalar privilegios

### 📂 Contenidos

| Archivo | Descripción |
|---------|-------------|
| **[WhereIsMyWebShell.md](WhereIsMyWebShell.md)** | Writeup profesional completo en español con explicaciones detalladas y recomendaciones de seguridad |
| **[QuickStart.md](QuickStart.md)** | Guía rápida de resolución para completar el laboratorio rápidamente |
| **[WhereIsMyWebShell-En.md](WhereIsMyWebShell-En.md)** | Writeup profesional completo en inglés con explicaciones detalladas y recomendaciones de seguridad |

### 🛠️ Herramientas y Tecnologías
- **Nmap**: Escaneo de puertos de red
- **Gobuster**: Fuzzing de directorios
- **FFuz**: Fuzzing de parámetros
- **Netcat (nc)**: Escuchador de shell inversa
- **Bash**: Scripting de shell

### 📊 Vulnerabilidades Principales Cubiertas
1. **Ejecución Remota de Comandos (RCE)**: Ejecución insegura de comandos a través de parámetros web
2. **Validación Insuficiente de Entrada**: Falta de sanitización de parámetros
3. **Manejo Inseguro de Archivos**: Exposición de archivos sensibles con permisos débiles
4. **Controles de Acceso Faltantes**: Acceso sin restricciones a funcionalidades sensibles

### 🔍 Fases del Ataque
1. **Reconocimiento**: Escaneo de puertos e identificación de servicios
2. **Enumeración**: Descubrimiento de directorios y parámetros mediante fuzzing
3. **Explotación**: RCE a través de parámetro vulnerable
4. **Escalada**: Establecimiento de shell inversa para elevación de privilegios
5. **Post-Explotación**: Extracción de credenciales y escalada de privilegios

### 🛡️ Recomendaciones de Seguridad
Este writeup incluye 5 recomendaciones de seguridad integrales para prevenir ataques de RCE y shells inversas:

1. Validación y Sanitización de Entrada
2. Prohibición de Funciones Peligrosas
3. Control de Acceso y Autenticación
4. Principio del Menor Privilegio
5. Implementación de Web Application Firewall (WAF)

### 💡 Resultados de Aprendizaje
Al completar este reto, comprenderás:
- Cómo las aplicaciones web pueden ser comprometidas mediante manipulación de parámetros
- La importancia de la validación de entrada en la seguridad web
- Cómo funcionan las shells inversas y sus implicaciones
- Técnicas de escalada de privilegios en entornos Linux
- Medidas defensivas contra ataques comunes de explotación web

---

## 🚀 Quick Start Guide / Guía de Inicio Rápido

For a quick overview of the resolution steps, see [QuickStart.md](QuickStart.md)

Para una descripción general rápida de los pasos de resolución, ver [QuickStart.md](QuickStart.md)

---

## 📖 How to Use This Writeup

1. **Quick Learning**: Start with [QuickStart.md](QuickStart.md) for a brief overview
2. **Detailed Study**: Read [WhereIsMyWebShell.md](WhereIsMyWebShell.md) (Spanish) or [WhereIsMyWebShell-En.md](WhereIsMyWebShell-En.md) (English) for comprehensive explanations
3. **Security Focus**: Review the security recommendations section for defensive strategies

---

## 🔗 Additional Resources

- [Dockerlabs Platform](https://dockerlabs.es)
- [OWASP: Command Injection](https://owasp.org/www-community/attacks/Command_Injection)
- [HackTricks: Reverse Shell](https://book.hacktricks.xyz/shells/shells/reverse-shells)
- [Linux Privilege Escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)

---

<div align="center">

**Happy Learning! / ¡Feliz Aprendizaje!** 🎓

*Last Updated: January 2026*

</div>
