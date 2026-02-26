# Rooted Penguin - Laboratorio de Seguridad en Aplicaciones Web

## 📋 Descripción Ejecutiva | Executive Summary

**Rooted Penguin** es un laboratorio de ciberseguridad avanzado diseñado para demostrar y explorar múltiples vulnerabilidades críticas en aplicaciones web modernas. Este desafío combina técnicas de pentesting contra una aplicación full-stack (React + Node.js) expuesta a través de vectores de ataque reales y prácticos.

**Rooted Penguin** is an advanced cybersecurity laboratory designed to demonstrate and explore multiple critical vulnerabilities in modern web applications. This challenge combines penetration testing techniques against a full-stack application (React + Node.js) exposed through real and practical attack vectors.

---

## 🎯 Objetivo del Laboratorio | Laboratory Objective

El objetivo es obtener acceso de **usuario root** en la máquina objetivo encadenando múltiples vulnerabilidades:

The objective is to obtain **root user** access on the target machine by chaining multiple vulnerabilities:

1. 🔍 **Information Disclosure** - Identificar usuarios válidos
2. 🔐 **Weak JWT Secret** - Descifrar la clave de firmado
3. 🎪 **Token Manipulation** - Forjar tokens de usuario elevado
4. 💾 **Insecure Storage** - Explotar localStorage del frontend
5. ⛔ **Broken Authorization** - Escalar privilegios
6. ⚠️ **XSS Vulnerability** - Inyección de scripts
7. 💥 **Command Injection** - Obtener reverse shell

---

## 📊 Vulnerabilidades Demostradas | Demonstrated Vulnerabilities

| # | Vulnerabilidad | Severidad | Descripción |
|---|---|---|---|
| 1 | Information Disclosure | 🔴 Alta | Revelación de usuarios vía registro |
| 2 | Weak JWT Secret | 🔴 Alta | Secret predecible por fuerza bruta |
| 3 | Token Manipulation | 🔴 Alta | Forjado de tokens JWT |
| 4 | Insecure localStorage | 🔴 Alta | Tokens sin protección en navegador |
| 5 | Broken Authorization | 🔴 Alta | Falta de validación en backend |
| 6 | Reflected XSS | 🔴 Alta | Inyección en campos de usuario |
| 7 | Command Injection | 🔴 CRÍTICA | Ejecución de comandos del SO |

---

## 🛠️ Requisitos Técnicos | Technical Requirements

### Software Obligatorio | Required Software
- **Nmap** - Escaneo de puertos | Port scanning
- **Burp Suite Community** - Análisis de tráfico HTTP | HTTP traffic analysis
- **John the Ripper** - Ataque de fuerza bruta | Brute force attack
- **curl o Postman** - Peticiones HTTP | HTTP requests
- **Netcat (nc)** - Reverse shell listener | Reverse shell listener
- **Docker** (si ejecuta localmente) - Para desplegar el laboratorio | To deploy the lab

### Hardware Recomendado | Recommended Hardware
- Mínimo 2GB RAM disponibles
- Procesador de 2+ cores
- 500MB espacio en disco

### Conocimientos Previos | Prerequisites
- HTML/CSS/JavaScript básico
- Conceptos de seguridad web (OWASP Top 10)
- Familiaridad con línea de comandos
- Entendimiento de JWT tokens
- Conocimientos de proxies HTTP

---

## 📚 Estructura de Archivos | File Structure

```
rootedPenguin-Dockerlabs/
├── rootedPenguin.md          # Análisis detallado (ES) | Detailed analysis (ES)
├── rootedPenguinEn.md        # Análisis detallado (EN) | Detailed analysis (EN)
├── QuickStart.md             # Guía rápida bilingüe | Bilingual quick guide
├── README.md                 # Este archivo | This file
├── img/                      # Carpeta de imágenes | Screenshots folder
│   ├── 1-nmap.png
│   ├── 2-http.png
│   ├── 2-ApisDocs.png
│   └── ... (más imágenes | more screenshots)
```

---

## 🚀 Cómo Comenzar | Getting Started

### Documentación Interactiva
Sin necesidad de desplegar, puede estudiar:
1. Leer `QuickStart.md` para una visión general rápida
2. Consultar `rootedPenguin.md` o `rootedPenguinEn.md` para análisis completo
3. Comprender cada paso de la explotación

### Laboratorio en Dockerlabs
Si está disponible en la plataforma Dockerlabs:
- Ingresar al desafío
- Esperar a que se inicie el contenedor
- Conectarse a `http://IP_ASIGNADA:5173`

---

## 📖 Documentación Disponible | Available Documentation

### 📄 Archivo Principal - Análisis Completo
- **[rootedPenguin.md](rootedPenguin.md)** - Documento maestro en español con:
  - Reconocimiento y enumeración
  - Descubrimiento de vulnerabilidades paso a paso
  - Pruebas de concepto (PoC)
  - Recomendaciones de mitigación detalladas
  - Capturas de pantalla anotadas

### 📄 English Version - Complete Analysis
- **[rootedPenguinEn.md](rootedPenguinEn.md)** - Master document in English featuring:
  - Reconnaissance and enumeration procedures
  - Step-by-step vulnerability discovery
  - Proof of Concept (PoC) demonstrations
  - Detailed mitigation recommendations
  - Annotated screenshots

### ⚡ Guía Rápida - Bilingual Quick Reference
- **[QuickStart.md](QuickStart.md)** - Para quienes quieren ir directo al grano:
  - 10 pasos críticos resumidos
  - Comandos clave
  - Tiempo estimado de resolución
  - Dos versiones: español e inglés

---

## 🎓 Objetivos de Aprendizaje | Learning Objectives

Después de completar este laboratorio, usted será capaz de:

✅ Entender los flujos de autenticación web modernos
✅ Identificar y explotar vulnerabilidades de JWT
✅ Realizar ataques de escalada de privilegios
✅ Ejecutar inyecciones de comandos del SO
✅ Analizar tráfico HTTP con herramientas profesionales
✅ Implementar pruebas de seguridad en aplicaciones web
✅ Redactar informes de vulnerabilidades profesionales

---

## 💡 Consejos Prácticos | Practical Tips

### Herramientas Útiles
- **Burp Suite** - Interceptación y manipulación de tráfico
- **JWT Debugger** - Decodificación de tokens
- **Base64 Encoder/Decoder** - Codificación de payloads
- **OWASP ZAP** - Alternativa libre a Burp Suite

### Metodología Recomendada
1. **Reconocimiento**: Mapear la aplicación completamente
2. **Enumeración**: Identificar usuarios y funciones
3. **Análisis**: Revisar tráfico con Burp Suite
4. **Explotación**: Ejecutar ataques según lo documentado
5. **Validación**: Confirmar el acceso obtenido
6. **Documentación**: Registrar todos los hallazgos

### Troubleshooting

**Problema**: El JWT no decodifica correctamente
- **Solución**: Verificar que tiene exactamente 3 partes separadas por puntos (.)

**Problema**: El payload de reverse shell no funciona
- **Solución**: Asegurarse de codificar en Base64 y URL-encode

**Problema**: No puede conectar a los puertos
- **Solución**: Verificar firewall y que Docker está ejecutándose

---

## 📝 Metodología OWASP | OWASP Methodology

Este laboratorio aborda las siguientes categorías del **OWASP Top 10**:

- **A01:2021** - Broken Access Control
- **A02:2021** - Cryptographic Failures
- **A03:2021** - Injection
- **A04:2021** - Insecure Design
- **A07:2021** - Cross-Site Scripting (XSS)

---

## 🔒 Mitigación de Vulnerabilidades | Vulnerability Mitigation

Para cada vulnerabilidad descubierta, se proporcionan:
- ✅ Descripción técnica
- ✅ Explicación del impacto
- ✅ Recomendaciones de corrección
- ✅ Ejemplos de código seguro (cuando aplica)

Consulte la sección **12. Recomendaciones de Mitigación** en los documentos principales.

---

## 📞 Soporte y Preguntas | Support and Questions

### Recursos Recomendados
- [OWASP Top 10](https://owasp.org/Top10/)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8949)
- [CWE/SANS Top 25](https://cwe.mitre.org/top25/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)

### Comunidades Útiles
- OWASP Local Chapters
- Bug Bounty Platforms
- Security Conferences
---

## ⚖️ Aviso Legal | Legal Notice

Este material está diseñado **exclusivamente con fines educativos**. El uso de esta información para acceder a sistemas sin autorización es **ilegal**. Siempre obtenga el consentimiento explícito antes de realizar pruebas de penetración.

This material is designed **exclusively for educational purposes**. Using this information to access systems without authorization is **illegal**. Always obtain explicit authorization before conducting penetration tests.

---

**¡Bienvenido al Laboratorio Rooted Penguin!**
**Welcome to Rooted Penguin Laboratory!**

Para comenzar, consulte [QuickStart.md](QuickStart.md) o lea el análisis completo en [rootedPenguin.md](rootedPenguin.md).
