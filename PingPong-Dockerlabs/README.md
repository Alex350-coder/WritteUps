# 🎯 Laboratorio PingPong - Dockerlabs

## English Version Below | Versión en Inglés Abajo ⬇️

---

## 📋 Descripción (Español)

**PingPong** es un laboratorio de ciberseguridad de nivel intermedio diseñado para enseñar técnicas de explotación y escalada de privilegios en sistemas Linux. 

El ejercicio comienza con la identificación de una vulnerabilidad de **inyección de comandos** en un servicio web, que permite ejecutar comandos arbitrarios en el servidor. A través de esta vulnerabilidad inicial, se obtiene acceso como usuario no privilegiado, desde donde se procede a una **escalada de privilegios encadenada** utilizando configuraciones inseguras de `sudo` y binarios explotables, hasta lograr acceso completo como usuario `root`.

### 🎓 Objetivos de Aprendizaje

- Identificar y explotar vulnerabilidades de **inyección de comandos**
- Comprender la importancia de la **validación de entrada**
- Analizar configuraciones inseguras de **control de acceso (`sudo`)**
- Explotar binarios del sistema mediante **GTFObins**
- Realizar **escalada de privilegios encadenada**
- Implementar **estrategias defensivas** contra estas vulnerabilidades

### 🔑 Vulnerabilidades Clave

1. **Inyección de Comandos** - Entrada no validada en el servicio ping
2. **Configuración Insegura de Sudo (NOPASSWD)** - Permisos delegados sin autenticación
3. **Binarios Explotables** - Herramientas del sistema con capacidades de ejecución de código
4. **Almacenamiento Inseguro de Credenciales** - Contraseñas en texto plano
5. **Arquitectura de Escalada Encadenada** - Múltiples puntos de acceso sin aislamiento

---

## 📦 Requisitos

### Mínimos
- **Máquina Atacante:** Linux con `nmap`, `netcat` y herramientas estándar
- **Máquina Objetivo:** Contenedor Docker o VM con servicios HTTP en puertos 80, 443, 5000
- **Experiencia Previa:** Conocimiento básico de Linux, bash y redes

### Herramientas Recomendadas
```bash
nmap              # Escaneo de puertos
netcat (nc)       # Listeners de shell reversa
python3           # Estabilización de shell
curl/wget         # Pruebas HTTP
GTFObins          # Referencia de explotación (https://gtfobins.github.io/)
```

---

## 🚀 Inicio Rápido

Para una guía paso a paso de resolución rápida, consulta **[QuickStart.md](QuickStart.md)**.

### Resumen de Pasos

1. **Escaneo de puertos** → Identificar servicios
2. **Pruebas de inyección de comandos** → Obtener acceso inicial
3. **Shell reversa** → Establecer conexión interactiva
4. **Escalada encadenada** → freddy → bobby → gladys → chocolatito → theboss → root

---

## 📚 Documentación

Este repositorio contiene la siguiente documentación:

- **[PingPong.md](PingPong.md)** - Análisis detallado del laboratorio en español con explicaciones técnicas, imágenes de referencia y recomendaciones de mitigación.
- **[PingPongEN.md](PingPongEN.md)** - Análisis completo en inglés con la misma cobertura técnica y recomendaciones de seguridad.
- **[QuickStart.md](QuickStart.md)** - Guía concisa bilingüe con los pasos críticos para completar el laboratorio rápidamente.
- **[README.md](README.md)** - Este archivo, proporciona una visión general del proyecto.

---

## 🛡️ Recomendaciones de Seguridad

Para mitigar las vulnerabilidades demostradas en este laboratorio:

### 1. Validación Estricta de Entrada
```bash
# ✗ INSEGURO
ping $user_input

# ✓ SEGURO
if [[ $user_input =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]; then
    ping "$user_input"
fi
```

### 2. Configuración Segura de Sudo
```bash
# ✗ INSEGURO
freddy ALL=(bobby) NOPASSWD: /usr/bin/dpkg

# ✓ SEGURO
freddy ALL=(bobby) /usr/bin/dpkg -l only
# O requiere contraseña
freddy ALL=(bobby) /usr/bin/dpkg
```

### 3. Gestión Segura de Secretos
- Utilizar gestores de secretos (HashiCorp Vault, Kubernetes Secrets)
- Nunca almacenar credenciales en archivos de texto plano
- Implementar rotación automática de credenciales

### 4. Auditoría y Monitoreo
```bash
# Monitorear cambios en sudoers
auditctl -w /etc/sudoers -p wa

# Registrar ejecución de binarios sensibles
auditctl -w /bin/bash -p x
auditctl -w /usr/bin/php -p x
```

---

## 👨‍💼 Sobre Este Laboratorio

**Nivel:** Intermedio  
**Duración Estimada:** 20-30 minutos (con documentación)  
**Plataforma:** Dockerlabs (https://www.dockerlabs.es/)  
**Tipo:** CTF educativo / Red Team Training

---

## 📖 Referencias

- **GTFObins:** https://gtfobins.github.io/ - Base de datos de explotaciones de binarios
- **OWASP CWE-78:** https://cwe.mitre.org/data/definitions/78.html - Inyección de comandos
- **Sudo Security:** https://www.sudo.ws/security/policy/ - Política de seguridad de sudo
- **Linux Privilege Escalation:** https://book.hacktricks.xyz/linux-hardening/privilege-escalation - Guía de escalada

---

## ⚖️ Aviso Legal

Este laboratorio es para **propósitos educativos únicamente**. Solo debe ejecutarse en:
- Entornos de prueba controlados
- Máquinas de las que seas propietario
- Sistemas con consentimiento explícito de autorización

El uso no autorizado de estas técnicas contra sistemas que no posees es **ilegal**.

---

---

## 📋 Description (English)

**PingPong** is an intermediate-level cybersecurity laboratory designed to teach exploitation and privilege escalation techniques in Linux systems.

The exercise begins with the identification of a **command injection vulnerability** in a web service, which allows execution of arbitrary commands on the server. Through this initial vulnerability, access is obtained as an unprivileged user, from which a **chained privilege escalation** is performed using insecure `sudo` configurations and exploitable binaries, until complete access is achieved as the `root` user.

### 🎓 Learning Objectives

- Identify and exploit **command injection vulnerabilities**
- Understand the importance of **input validation**
- Analyze insecure **access control configurations (`sudo`)**
- Exploit system binaries via **GTFObins**
- Perform **chained privilege escalation**
- Implement **defensive strategies** against these vulnerabilities

### 🔑 Key Vulnerabilities

1. **Command Injection** - Unvalidated input in ping service
2. **Insecure Sudo Configuration (NOPASSWD)** - Delegated permissions without authentication
3. **Exploitable Binaries** - System tools with code execution capabilities
4. **Insecure Credential Storage** - Plaintext passwords
5. **Chained Escalation Architecture** - Multiple access points without isolation

---

## 📦 Requirements

### Minimum
- **Attacker Machine:** Linux with `nmap`, `netcat`, and standard tools
- **Target Machine:** Docker container or VM with HTTP services on ports 80, 443, 5000
- **Prior Experience:** Basic knowledge of Linux, bash, and networking

### Recommended Tools
```bash
nmap              # Port scanning
netcat (nc)       # Reverse shell listeners
python3           # Shell stabilization
curl/wget         # HTTP testing
GTFObins          # Exploitation reference (https://gtfobins.github.io/)
```

---

## 🚀 Quick Start

For a step-by-step guide for rapid resolution, see **[QuickStart.md](QuickStart.md)**.

### Steps Summary

1. **Port scanning** → Identify services
2. **Command injection testing** → Gain initial access
3. **Reverse shell** → Establish interactive connection
4. **Chained escalation** → freddy → bobby → gladys → chocolatito → theboss → root

---

## 📚 Documentation

This repository contains the following documentation:

- **[PingPong.md](PingPong.md)** - Detailed laboratory analysis in Spanish with technical explanations, reference images, and mitigation recommendations.
- **[PingPongEN.md](PingPongEN.md)** - Complete analysis in English with the same technical coverage and security recommendations.
- **[QuickStart.md](QuickStart.md)** - Concise bilingual guide with critical steps to complete the laboratory quickly.
- **[README.md](README.md)** - This file, provides a project overview.

---

## 🛡️ Security Recommendations

To mitigate the vulnerabilities demonstrated in this laboratory:

### 1. Strict Input Validation
```bash
# ✗ INSECURE
ping $user_input

# ✓ SECURE
if [[ $user_input =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]; then
    ping "$user_input"
fi
```

### 2. Secure Sudo Configuration
```bash
# ✗ INSECURE
freddy ALL=(bobby) NOPASSWD: /usr/bin/dpkg

# ✓ SECURE
freddy ALL=(bobby) /usr/bin/dpkg -l only
# Or requires password
freddy ALL=(bobby) /usr/bin/dpkg
```

### 3. Secure Secrets Management
- Use secrets managers (HashiCorp Vault, Kubernetes Secrets)
- Never store credentials in plaintext files
- Implement automatic credential rotation

### 4. Auditing and Monitoring
```bash
# Monitor sudoers changes
auditctl -w /etc/sudoers -p wa

# Log execution of sensitive binaries
auditctl -w /bin/bash -p x
auditctl -w /usr/bin/php -p x
```

---

## 👨‍💼 About This Laboratory

**Level:** Intermediate  
**Estimated Duration:** 20-30 minutes (with documentation)  
**Platform:** Dockerlabs (https://www.dockerlabs.es/)  
**Type:** Educational CTF / Red Team Training

---

## 📖 References

- **GTFObins:** https://gtfobins.github.io/ - Database of binary exploitation techniques
- **OWASP CWE-78:** https://cwe.mitre.org/data/definitions/78.html - Command Injection
- **Sudo Security:** https://www.sudo.ws/security/policy/ - Sudo security policy
- **Linux Privilege Escalation:** https://book.hacktricks.xyz/linux-hardening/privilege-escalation - Escalation guide

---

## ⚖️ Legal Notice

This laboratory is for **educational purposes only**. It should only be executed in:
- Controlled test environments
- Machines you own
- Systems with explicit authorization consent

Unauthorized use of these techniques against systems you don't own is **illegal**.

---

## 📞 Support

For questions or issues regarding this laboratory, refer to the detailed documentation in [PingPong.md](PingPong.md) or [PingPongEN.md](PingPongEN.md).

---

**Last Updated:** May 2026  
**Status:** Complete and Ready for Use
