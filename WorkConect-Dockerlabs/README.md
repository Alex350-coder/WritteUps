# Work Conect - Professional Security Lab

![Status: Active](https://img.shields.io/badge/Status-Active-success)
![Difficulty: Medium](https://img.shields.io/badge/Difficulty-Medium-orange)
![Platform: Dockerlabs](https://img.shields.io/badge/Platform-Dockerlabs-blue)

---

## 🇪🇸 DESCRIPCIÓN EN ESPAÑOL

### Resumen General

**Work Conect** es un laboratorio interactivo de ciberseguridad que proporciona un entorno vulnerable realista para el aprendizaje y la práctica de técnicas de penetración. Simula una plataforma de red social profesional donde los participantes pueden explorar múltiples vectores de ataque comunes en aplicaciones web modernas.

### Objetivos del Laboratorio

El laboratorio está diseñado para que los participantes:

1. **Demostrar** comprensión de inyección de comandos y sus impactos
2. **Explorar** exposición de información sensible en aplicaciones web
3. **Practicar** técnicas de escalada de privilegios en Linux
4. **Comprender** la importancia de validación de entrada y control de acceso
5. **Aprender** cadenas de ataque multi-etapa en entornos reales

### Vulnerabilidades Principales

| Vulnerabilidad | CWE | Severidad | Descripción |
|---|---|---|---|
| **Inyección de Comandos** | CWE-78 | 🔴 Crítica | Ejecución arbitraria de comandos del sistema |
| **Exposición de Información** | CWE-200 | 🔴 Crítica | Acceso a datos sensibles no protegidos |
| **Validación Débil de Cargas** | CWE-434 | 🟠 Alta | Falta de validación de archivos subidos |
| **Control de Acceso Impropio** | CWE-284 | 🟠 Alta | Acceso a datos y funciones de otros usuarios |
| **Escalada de Privilegios** | CWE-269 | 🔴 Crítica | Ejecución de tareas con privilegios elevados |

### Requisitos

#### Mínimos
- **Sistema Operativo:** Linux/Windows/macOS
- **Docker:** Versión 20.10+
- **Docker Compose:** Versión 1.29+
- **Herramientas Recomendadas:**
  - `nmap` - Escaneo de puertos
  - `netcat (nc)` - Reverse shell
  - `curl` - Pruebas HTTP
  - Navegador web (Firefox/Chrome)

#### Recomendados
- VirtualBox o VMware (para aislamiento de red)
- Máquina virtual Kali Linux o Parrot OS
- Burp Suite Community (análisis de tráfico)

### Información de Laboratorio

- **URL:** `http://localhost:8000`
- **Documentación de API:** `http://localhost:8000/docs`
- **Puerto de Servicio:** 8000
- **Duración Estimada:** 30-60 minutos
- **Nivel de Dificultad:** Medio (⭐⭐⭐)

### Documentación

| Documento | Descripción |
|---|---|
| [workConect.md](workConect.md) | Writeup detallado del laboratorio en español |
| [workConectEN.md](workConectEN.md) | Writeup completo en inglés |
| [QuickStart.md](QuickStart.md) | Guía rápida de pasos críticos (bilingüe) |

### Pasos Iniciales

1. **Reconocimiento:** Ejecutar `nmap -p- localhost`
2. **Acceso:** Crear cuenta y acceder a la plataforma
3. **Análisis:** Explorar funcionalidades de la aplicación
4. **Explotación:** Identificar y explotar vulnerabilidades
5. **Escalada:** Obtener acceso privilegiado
6. **Objetivo Final:** Obtener acceso root

Para detalles completos, consultar [QuickStart.md](QuickStart.md)

### Casos de Uso

Este laboratorio es ideal para:

- 🎓 **Estudiantes de Ciberseguridad:** Aprendizaje práctico de vulnerabilidades web
- 👨‍💼 **Profesionales de Seguridad:** Entrenamiento en técnicas de pentesting
- 🔐 **Desarrolladores:** Comprensión de vulnerabilidades y prácticas seguras
- 🏢 **Equipos Red Team:** Simulación de ataques realistas

### Advertencia Legal

**IMPORTANTE:** Este laboratorio debe utilizarse únicamente en:

- Entornos de laboratorio controlados
- Sistemas que usted posee o tiene permiso explícito para probar
- Propósitos educativos y de entrenamiento

El acceso no autorizado a sistemas informáticos es **ILEGAL**. Los autores no se responsabilizan por el mal uso de este material.

### Soporte y Contribuciones

Para reportes de errores, sugerencias o contribuciones:
- Abrir un issue en el repositorio
- Enviar pull requests con mejoras

---

## 🇬🇧 ENGLISH DESCRIPTION

### General Overview

**Work Conect** is an interactive cybersecurity laboratory that provides a realistic vulnerable environment for learning and practicing penetration testing techniques. It simulates a professional social networking platform where participants can explore multiple common attack vectors in modern web applications.

### Laboratory Objectives

The laboratory is designed for participants to:

1. **Demonstrate** understanding of command injection and its impacts
2. **Explore** sensitive information exposure in web applications
3. **Practice** privilege escalation techniques on Linux
4. **Understand** the importance of input validation and access control
5. **Learn** multi-stage attack chains in realistic environments

### Primary Vulnerabilities

| Vulnerability | CWE | Severity | Description |
|---|---|---|---|
| **Command Injection** | CWE-78 | 🔴 Critical | Arbitrary system command execution |
| **Information Disclosure** | CWE-200 | 🔴 Critical | Access to unprotected sensitive data |
| **Weak Upload Validation** | CWE-434 | 🟠 High | Lack of file upload validation |
| **Improper Access Control** | CWE-284 | 🟠 High | Access to other users' data and functions |
| **Privilege Escalation** | CWE-269 | 🔴 Critical | Execution of tasks with elevated privileges |

### Requirements

#### Minimum
- **Operating System:** Linux/Windows/macOS
- **Docker:** Version 20.10+
- **Docker Compose:** Version 1.29+
- **Recommended Tools:**
  - `nmap` - Port scanning
  - `netcat (nc)` - Reverse shells
  - `curl` - HTTP testing
  - Web browser (Firefox/Chrome)

#### Recommended
- VirtualBox or VMware (for network isolation)
- Virtual machine with Kali Linux or Parrot OS
- Burp Suite Community (traffic analysis)

### Laboratory Information

- **URL:** `http://localhost:8000`
- **API Documentation:** `http://localhost:8000/docs`
- **Service Port:** 8000
- **Estimated Duration:** 30-60 minutes
- **Difficulty Level:** Medium (⭐⭐⭐)

### Documentation

| Document | Description |
|---|---|
| [workConect.md](workConect.md) | Detailed laboratory writeup in Spanish |
| [workConectEN.md](workConectEN.md) | Complete writeup in English |
| [QuickStart.md](QuickStart.md) | Quick guide of critical steps (bilingual) |

### Getting Started

1. **Reconnaissance:** Run `nmap -p- localhost`
2. **Access:** Create account and log in to the platform
3. **Analysis:** Explore application features
4. **Exploitation:** Identify and exploit vulnerabilities
5. **Escalation:** Obtain privileged access
6. **Final Goal:** Achieve root access

For complete details, see [QuickStart.md](QuickStart.md)

### Use Cases

This laboratory is ideal for:

- 🎓 **Cybersecurity Students:** Practical learning of web vulnerabilities
- 👨‍💼 **Security Professionals:** Penetration testing technique training
- 🔐 **Developers:** Understanding of vulnerabilities and secure practices
- 🏢 **Red Team:** Realistic attack simulation

### Legal Warning

**IMPORTANT:** This laboratory should only be used in:

- Controlled laboratory environments
- Systems you own or have explicit permission to test
- Educational and training purposes

Unauthorized access to computer systems is **ILLEGAL**. The authors are not responsible for misuse of this material.

### Support and Contributions

For bug reports, suggestions, or contributions:
- Open an issue in the repository
- Submit pull requests with improvements

---

## 📚 Recursos Adicionales / Additional Resources

### Español
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [Guía de Pruebas OWASP](https://owasp.org/www-project-web-security-testing-guide/)

### English
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

---

**Última Actualización / Last Update:** Mayo 2026 / May 2026

**Versión / Version:** 1.0

**Licencia / License:** Educational Use Only
