# Canary - Dockerlabs Security Laboratory


---

## Contenido en Español

### 📋 Introducción

**Canary** es un laboratorio de seguridad ofensiva de nivel avanzado diseñado para enseñar técnicas sofisticadas de explotación de sistemas. Este laboratorio simula un escenario real donde los defensores han implementado múltiples capas de protección, incluyendo Stack Canary, protecciones basadas en permisos y validación de entrada. Los atacantes deben utilizar técnicas avanzadas de ingeniería inversa, análisis binario y exploración de vulnerabilidades encadenadas para lograr el objetiv de escalada de privilegios hasta obtener acceso de administrador.

### 🎯 Objetivo del Laboratorio

El objetivo principal es:
1. Obtener acceso inicial mediante exploit de upload de archivos
2. Escalar privilegios desde usuario www-data a usuario jerry
3. Analizar binarios vulnerables mediante ingeniería inversa
4. Eludir protecciones de Stack Canary mediante vulnerabilidades de Format String
5. Ejecutar buffer overflow para redirigir ejecución
6. Obtener acceso de administrador (root) del sistema

### 📚 Vulnerabilidades Abordadas

- **File Upload Bypass**: Validación insuficiente de extensiones de archivo
- **Insecure sudo Configuration**: Permisos que permiten escalada a través de editores de texto
- **Format String Vulnerability**: Lectura de memoria mediante especificadores de formato
- **Stack Canary Bypass**: Elusión de protecciones de corrupción de pila
- **Buffer Overflow**: Desbordamiento de búfer en binarios compilados
- **Privilege Escalation**: Escalada de privilegios mediante binarios SUID/sudo

### ✅ Requisitos Previos

**Hardware**:
- 2 GB de RAM mínimo
- 10 GB de espacio en disco disponible
- Procesador de 2 GHz o superior

**Software**:
- Docker y Docker Compose instalados
- Herramientas de penetración: nmap, gobuster, curl, wget
- Herramientas de depuración: GDB, Ghidra
- Intérprete Python 3.8+
- Librería pwntools para Python

**Conocimiento Previo Recomendado**:
- Fundamentos de seguridad ofensiva
- Conceptos de explotación de binarios
- Conocimiento básico de C y ensamblador x86-64
- Experiencia con herramientas de análisis binario

### 📖 Documentación

Esta carpeta contiene la siguiente documentación:

1. **Canary.md** - Write-up detallado en español con análisis paso a paso
2. **CanaryEN.md** - English version del write-up completo
3. **QuickStart.md** - Guía de inicio rápido bilingüe con todos los comandos esenciales
4. **README.md** - Este archivo con información general del laboratorio

### 🛡️ Protecciones Implementadas

1. **Stack Canary**: Protección contra desbordamiento de pila
2. **ASLR** (opcional): Aleatorización del layout de memoria
3. **DEP/NX**: Prevención de ejecución de datos
4. **Validación de Extensiones**: Whitelist de extensiones permitidas
5. **Permisos restrictivos**: Configuración de sudoers con privilegios mínimos

### 📊 Dificultad y Duración

- **Nivel de Dificultad**: Avanzado (Hard)
- **Tiempo Estimado**: 45-90 minutos
- **Requisitos Previos**: Experiencia intermedia-avanzada en hacking ético

### 💡 Conceptos de Aprendizaje

#### Técnicas de Explotación
- Bypass de filtros y validaciones
- Análisis de control de flujo en binarios
- Lectura de memoria mediante vulnerabilidades de formato
- Construcción de payloads para buffer overflow
- Manipulación de stack frames

#### Defensa y Mitigación
- Implementación de Stack Canary
- Validación robusta de entrada
- Configuración segura de sudoers
- Compilación segura de código C
- Auditoría de permisos de archivo

### 🔗 Enlaces Útiles

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Buffer Overflow - GeeksforGeeks](https://www.geeksforgeeks.org/buffer-overflow/)
- [Format String Vulnerabilities](https://owasp.org/www-community/attacks/Format_string_attack)
- [Stack Canary Protection](https://en.wikipedia.org/wiki/Stack_guard)
- [Ghidra Documentation](https://ghidra-sre.org/)

### 📝 Notas Importantes

- Este laboratorio es para **fines educativos exclusivamente**
- Debe utilizarse en entorniamiento controlados y autorizados
- No intente realizar las técnicas enseñadas en sistemas sin autorización
- Mantener logs de auditoría para fines de documentación

### 🏆 Indicadores de Éxito

Has completado exitosamente el laboratorio cuando:
- ✅ Acceso inicial obtenido como www-data
- ✅ Escalada a usuario jerry completada
- ✅ Funciones ocultas identificadas en binarios
- ✅ Stack Canary bypasseado mediante Format String
- ✅ Buffer overflow ejecutado exitosamente
- ✅ Acceso root obtenido del sistema

### 🤝 Contribuciones y Soporte

Para reportar problemas, sugerencias o contribuciones:
- Crear un issue en el repositorio
- Proporcionar detalles completos del problema
- Incluir versiones de todas las herramientas utilizadas

### 📄 Licencia

Este laboratorio está disponible bajo licencia educativa con fines de aprendizaje.

---

## English Content

### 📋 Introduction

**Canary** is an advanced-level offensive security laboratory designed to teach sophisticated system exploitation techniques. This laboratory simulates a real-world scenario where defenders have implemented multiple layers of protection, including Stack Canary, permission-based protections, and input validation. Attackers must use advanced techniques in reverse engineering, binary analysis, and vulnerability chaining to achieve the goal of privilege escalation to obtain administrator access.

### 🎯 Laboratory Objective

The main objectives are:
1. Obtain initial access through file upload exploitation
2. Escalate privileges from www-data user to jerry user
3. Analyze vulnerable binaries through reverse engineering
4. Bypass Stack Canary protections using Format String vulnerabilities
5. Execute buffer overflow to redirect execution
6. Obtain administrator (root) access to the system

### 📚 Vulnerabilities Addressed

- **File Upload Bypass**: Insufficient file extension validation
- **Insecure sudo Configuration**: Permissions allowing escalation through text editors
- **Format String Vulnerability**: Memory reading through format specifiers
- **Stack Canary Bypass**: Bypassing stack corruption protections
- **Buffer Overflow**: Buffer overflow in compiled binaries
- **Privilege Escalation**: Privilege escalation through SUID/sudo binaries

### ✅ Prerequisites

**Hardware**:
- 2 GB RAM minimum
- 10 GB available disk space
- 2 GHz or higher processor

**Software**:
- Docker and Docker Compose installed
- Penetration tools: nmap, gobuster, curl, wget
- Debugging tools: GDB, Ghidra
- Python 3.8+ interpreter
- pwntools library for Python

**Recommended Prior Knowledge**:
- Offensive security fundamentals
- Binary exploitation concepts
- Basic C and x86-64 assembly knowledge
- Experience with binary analysis tools

### 📖 Documentation

This folder contains the following documentation:

1. **Canary.md** - Detailed write-up in Spanish with step-by-step analysis
2. **CanaryEN.md** - English version of the complete write-up
3. **QuickStart.md** - Quick start guide with bilingual essential commands
4. **README.md** - This file with general laboratory information

### 🛡️ Implemented Protections

1. **Stack Canary**: Protection against stack buffer overflow
2. **ASLR** (optional): Memory layout randomization
3. **DEP/NX**: Data execution prevention
4. **Extension Validation**: Whitelist of allowed extensions
5. **Restrictive Permissions**: Sudoers configuration with minimal privileges

### 📊 Difficulty and Duration

- **Difficulty Level**: Advanced (Hard)
- **Estimated Time**: 45-90 minutes
- **Prerequisites**: Intermediate-advanced ethical hacking experience

### 💡 Learning Concepts

#### Exploitation Techniques
- Bypass of filters and validations
- Control flow analysis in binaries
- Memory reading through format vulnerabilities
- Buffer overflow payload construction
- Stack frame manipulation

#### Defense and Mitigation
- Stack Canary implementation
- Robust input validation
- Secure sudoers configuration
- Secure C code compilation
- File permission auditing

### 🔗 Useful Links

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Buffer Overflow - GeeksforGeeks](https://www.geeksforgeeks.org/buffer-overflow/)
- [Format String Vulnerabilities](https://owasp.org/www-community/attacks/Format_string_attack)
- [Stack Canary Protection](https://en.wikipedia.org/wiki/Stack_guard)
- [Ghidra Documentation](https://ghidra-sre.org/)

### 📝 Important Notes

- This laboratory is for **educational purposes exclusively**
- Must be used in controlled and authorized environments
- Do not attempt to use taught techniques on systems without authorization
- Maintain audit logs for documentation purposes

### 🏆 Success Indicators

You have successfully completed the laboratory when:
- ✅ Initial access obtained as www-data
- ✅ Escalation to jerry user completed
- ✅ Hidden functions identified in binaries
- ✅ Stack Canary bypassed through Format String
- ✅ Buffer overflow successfully executed
- ✅ Root access to the system obtained

### 🤝 Contributions and Support

To report issues, suggestions, or contributions:
- Create an issue in the repository
- Provide complete details of the problem
- Include versions of all tools used

### 📄 License

This laboratory is available under educational license for learning purposes.

---

## 📞 Contact & Support

Para preguntas en español / For English questions:
- Revisar la documentación detallada en Canary.md / CanaryEN.md
- Consultar la guía rápida en QuickStart.md
- Refer to detailed documentation in Canary.md / CanaryEN.md
- Check quick guide in QuickStart.md

---

**Última actualización / Last Update**: March 4, 2026
