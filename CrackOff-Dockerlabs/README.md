# CrackOff - Laboratorio de Hacking Integral Dockerlabs

## Descripción del Proyecto | Project Description

**Español:** CrackOff es un laboratorio comprehensivo de seguridad cibernética diseñado para educadores y profesionales de penetration testing. El laboratorio simula un escenario de ataque realista donde los participantes deben ejecutar múltiples vectores de explotación encadenados, desde el reconocimiento inicial hasta la escalada de privilegios. A través de este desafío, se demuestran vulnerabilidades críticas como SQL injection, ataques de fuerza bruta, explotación de servicios web y técnicas avanzadas de escalada de privilegios.

**English:** CrackOff is a comprehensive cybersecurity laboratory designed for educators and penetration testing professionals. The laboratory simulates a realistic attack scenario where participants must execute multiple chained exploitation vectors, from initial reconnaissance to privilege escalation. Through this challenge, critical vulnerabilities are demonstrated, including SQL injection, brute-force attacks, web service exploitation, and advanced privilege escalation techniques.

---

## Objetivos de Aprendizaje | Learning Objectives

### Español
- Comprender el reconocimiento activo y pasivo de objetivos
- Identificar y explotar vulnerabilidades de SQL injection
- Ejecutar ataques de fuerza bruta contra servicios SSH
- Configurar y utilizar port forwarding para acceder a servicios internos
- Explotar servicios web como Apache Tomcat
- Implementar técnicas de reverse shell para obtener acceso remoto
- Escalar privilegios mediante sobrescritura de binarios
- Mantener la persistencia y acceso en múltiples cuentas de usuario

### English
- Understand active and passive target reconnaissance
- Identify and exploit SQL injection vulnerabilities
- Execute brute-force attacks against SSH services
- Configure and utilize port forwarding to access internal services
- Exploit web services such as Apache Tomcat
- Implement reverse shell techniques to obtain remote access
- Escalate privileges through binary overwrite techniques
- Maintain persistence and access across multiple user accounts

---

## Requisitos Técnicos | Technical Requirements

### Ambiente de Laboratorio | Laboratory Environment
- **Plataforma:** Dockerlabs (o equivalente Docker)
- **Sistema Operativo Atacante:** Linux (Kali Linux recomendado)
- **Dirección IP:** Será asignada dinámicamente por Dockerlabs

### Herramientas Requeridas | Required Tools
| Herramienta | Versión Mínima | Propósito |
|---|---|---|
| nmap | 7.80+ | Port scanning |
| gobuster | 3.1+ | Directory enumeration |
| sqlmap | 1.4+ | SQL injection exploitation |
| hydra | 9.0+ | Brute-force attacks |
| msfvenom | 6.0+ | Payload generation |
| netcat | 1.10+ | Reverse shell listener |
| OpenSSH | 7.4+ | SSH connection & port forwarding |
| scp | 7.4+ | Secure file transfer |
| Keepass2 | 2.47+ | Database decryption |

### Conocimientos Previos Recomendados | Recommended Prior Knowledge
- Conceptos fundamentales de redes TCP/IP
- Familiaridad con Linux/Unix y línea de comandos
- Comprensión básica de protocolos como SSH, HTTP, SQL
- Experiencia con penetration testing es beneficiosa pero no obligatoria

---

## Contenido del Laboratorio | Laboratory Content

Este repositorio contiene la siguiente documentación:

### Documentos Principales | Main Documents

1. **CrackOff.md** - Writeup Detallado en Español
   - Análisis paso a paso de la explotación
   - Técnicas y comandos específicos
   - Explicación de vulnerabilidades
   - Estructura jerárquica para fácil navegación
   - Incluye recomendaciones de mitigación al final del documento

2. **CrackOffEn.md** - Detailed Writeup in English
   - Complete English translation of the Spanish version
   - Technical terminology in English
   - All exploitation steps and techniques
   - Professional structure for easy reference
   - Includes mitigation recommendations at the end of the document

3. **QuickStart.md** - Guía de Inicio Rápido | Quick Reference Guide
   - Comandos esenciales en español e inglés
   - Pasos críticos para resolver el laboratorio
   - Tabla de herramientas utilizadas
   - Formato bilingüe para rápida consulta

4. **README.md** - Este archivo | This file
   - Presentación general del laboratorio
   - Requisitos técnicos
   - Instrucciones de uso
   - Referencias y recursos

### Carpeta de Imágenes | Images Folder
- La carpeta `img/` contiene todas las capturas de pantalla documentando cada fase del ataque
- Total de 25 imágenes de referencia
- Proporcionan evidencia visual de cada paso del proceso de explotación

---

## Guía de Uso | Usage Guide

### Español

#### Para Aprendices
1. Leer inicialmente el archivo `QuickStart.md` para entender los pasos generales
2. Comenzar el laboratorio y reproducir los ataques sin consultar el writeup detallado
3. Cuando encuentres dificultades, consultar secciones específicas en `CrackOff.md`
4. Todas las imágenes en la carpeta `img/` sirven como referencia visual

#### Para Educadores
1. Compartir `QuickStart.md` como referencia rápida
2. Utilizar `CrackOff.md` como materiales de clase
3. Las imágenes pueden incluirse en presentaciones educativas
4. Adaptar el contenido según necesidades del currículo

### English

#### For Learners
1. Initially read `QuickStart.md` to understand general steps
2. Start the laboratory and reproduce attacks without consulting detailed writeup
3. When encountering difficulties, reference specific sections in `CrackOffEn.md`
4. All images in the `img/` folder serve as visual references

#### For Educators
1. Share `QuickStart.md` as a quick reference
2. Use `CrackOffEn.md` as class materials
3. Images can be included in educational presentations
4. Adapt content according to curriculum needs

---

## Estructura de Fases | Phase Structure

El laboratorio está organizado en 7 fases principales:

| Fase | Objetivo | Vulnerabilidades |
|------|----------|------------------|
| 1. Reconocimiento | Identificar servicios | Port scanning, Web enumeration |
| 2. SQLi | Extraer datos | SQL injection |
| 3. Acceso SSH | Obtener acceso inicial | Weak credentials, Brute-force |
| 4. Descubrimiento | Identificar servicios internos | Lack of access controls |
| 5. Tomcat | RCE via web service | Weak auth, Insecure upload |
| 6. Escalada | Obtener acceso root | Binary overwrite, Sudo misconfiguration |
| 7. Bonus | Acceso adicional | Credential storage, Encryption keys |

---

## Advertencia Legal | Legal Notice

### Español
Este laboratorio está diseñado exclusivamente para fines educativos y de investigación en ambientes autorizados. El uso no autorizado de estas técnicas contra sistemas sin consentimiento es ilegal. Los usuarios son responsables de cumplir con todas las leyes y regulaciones aplicables en sus jurisdicciones.

### English
This laboratory is designed exclusively for educational and research purposes in authorized environments. Unauthorized use of these techniques against systems without consent is illegal. Users are responsible for complying with all applicable laws and regulations in their jurisdictions.

---

## Recursos Adicionales | Additional Resources

### Documentación Oficial
- [Kali Linux Tools](https://www.kali.org/tools/)
- [SQLmap Documentation](http://sqlmap.org/)
- [Metasploit Framework](https://docs.metasploit.com/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)

### Plataformas de Aprendizaje
- **Dockerlabs:** Laboratorios de hacking en contenedores
- **HackTheBox:** Plataforma de CTF y penetration testing
- **TryHackMe:** Cursos interactivos de seguridad
- **PortSwigger Web Security Academy:** Capacitación en seguridad web

### Referencias de Seguridad
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [CWE - Common Weakness Enumeration](https://cwe.mitre.org/)
- [CVE - Common Vulnerabilities and Exposures](https://cve.mitre.org/)

---

## Estructura de Archivos | File Structure

```
CrackOff-Dockerlabs/
├── README.md                 # Este archivo
├── CrackOff.md              # Writeup detallado (Español)
├── CrackOffEn.md            # Writeup detallado (English)
├── QuickStart.md            # Guía rápida (Bilingual)
├── img/
│   ├── 1-Nmap.png
│   ├── 2-gobuster.png
│   ├── 3-http.png
│   ├── 4httpLoghin.png
│   ├── 5-httpWelcome.png
│   ├── 6-SQLiVulnerable.png
│   ├── 7-dbsSQLi.png
│   ├── 8-tablesSQLi.png
│   ├── 9-CrackOFFUsers.png
│   ├── 9-CrackOffPasswords.png
│   ├── 10-CrackOFFtrueTable.png
│   ├── 11-hydra.png
│   ├── 12-sshRosa.png
│   ├── 13-CommonVectorsRosa.png
│   ├── 14-CatEtcPasswd.png
│   ├── 15-AliceNote.png
│   ├── 16-netstatAno.png
│   ├── 16-PortFowd.png
│   ├── 17-TomcatHttp.png
│   ├── 17-TomcatHydra.png
│   ├── 18-ManagerTomcat.png
│   ├── 19-ReverseShellTomcat.png
│   ├── 20-Catalina-Root.png
│   ├── 21-SshMario.png
│   ├── 22-FlagMario.png
│   ├── 23-scpAlice.png
│   ├── 24-AlicePassword.png
│   └── 25-sshAlice.png
```

---

## Contribuciones y Mejoras | Contributions and Improvements

Este proyecto es una documentación educativa. Se aceptan contributions para:
- Correcciones ortográficas o gramaticales
- Clarificación de pasos técnicos
- Traducciones adicionales
- Ejemplos alternativos de explotación
- Notas sobre defensas y mitigaciones

---

**Última Actualización | Last Updated:** Febrero 2026
**Versión | Version:** 2.0
**Estado | Status:** Documentación Completa | Complete Documentation
