# SECorNOTsec - Dockerlabs Security Challenge

---

## ESPAÑOL

### 📋 Descripción General

**SECorNOTsec** es un laboratorio de seguridad ofensiva de nivel medio alojado en Dockerlabs que presenta una cadena realista de vulnerabilidades. El objetivo es demostrar cómo múltiples fallos de seguridad, aunque no sean críticos individualmente, pueden encadenarse para lograr un compromiso total del sistema.

### 🎯 Objetivo del Laboratorio

Obtener acceso de administrador y, posteriormente, acceso root al sistema objetivo mediante la explotación de seis vulnerabilidades correlacionadas:

1. **Gestión insegura de secretos** - Exposición de archivos de respaldo
2. **Criptografía débil** - Vector de Inicialización estático
3. **Falsificación de sesiones** - Cookie forging con JWTs
4. **Inyección de comandos** - RCE con WAF bypass
5. **Control de acceso débil** - Permisos sudo excesivos
6. **Vulnerabilidades de privación de librería** - LD_PRELOAD exploitation

### 🔍 Vulnerabilidades Clave

| Vulnerabilidad | CVSS | Tipo | Impacto |
|---|---|---|---|
| Exposición de env.bak | 7.5 | CWE-541 | Revelación de secretos |
| IV estático | 5.3 | CWE-327 | Debilitamiento de cifrado |
| Command Injection | 9.8 | CWE-78 | Ejecución remota de código |
| Sudo misconfiguration | 8.8 | CWE-276 | Escalada de privilegios |
| LD_PRELOAD abuse | 9.0 | CWE-94 | Escalada a root |

### 📦 Requisitos

- **Entorno:** Docker / Dockerlabs
- **Herramientas recomendadas:**
  - nmap (reconocimiento de puertos)
  - gobuster (fuzzing de directorios)
  - Python 3.x (scripting de cookies)
  - curl / Burp Suite (inspección web)
  - chmod, sudo, find (comandos del sistema)
  
- **Conocimientos previos:**
  - Fundamentos de redes TCP/IP
  - Comandos básicos de Linux
  - Conceptos de autenticación y sesiones
  - Principios básicos de criptografía

### 🚀 Cómo Comenzar

1. **Identificar el objetivo:**
   ```bash
   nmap -p- <target_ip>
   ```

2. **Análisis rápido:**
   - Consulta [QuickStart.md](QuickStart.md) para pasos críticos
   
3. **Análisis detallado:**
   - Consulta [SECorNOTsec.md](SECorNOTsec.md) para writeup completo

### 📚 Documentación

- **[SECorNOTsec.md](SECorNOTsec.md)** - Análisis técnico completo en español (con imágenes)
- **[SECorNOTsecEn.md](SECorNOTsecEn.md)** - Technical analysis in English (with images)
- **[QuickStart.md](QuickStart.md)** - Guía rápida bilingüe
- **`img/`** - Directorio con capturas de pantalla numeradas

### 💡 Conceptos Aprendidos

Después de completar este laboratorio, comprenderás:

✅ Gestión segura de secretos y configuración  
✅ Impacto de parámetros criptográficos débiles  
✅ Explotación de cookies JWT sin validación robusta  
✅ Técnicas de WAF bypass y ofuscación  
✅ Principio del menor privilegio en sudoers  
✅ Vulnerabilidades de LD_PRELOAD y cómo mitigarlas  
✅ Cadenas de escalada de privilegios  

### 🏆 Dificultad Estimada

- **Reconocimiento:** ⭐ (Fácil)
- **Inyección de comandos:** ⭐⭐ (Medio)
- **Escalada sudo:** ⭐⭐⭐ (Difícil)
- **LD_PRELOAD:** ⭐⭐ (Medio)
- **Tiempo total:** 20-40 minutos

### ⚠️ Notas Legales

Este laboratorio está diseñado **únicamente para propósitos educativos** en entornos autorizados. Cualquier acceso no autorizado a sistemas informáticos es ilegal.

### 📞 Recursos Adicionales

- [OWASP Top 10 Vulnerabilities](https://owasp.org/www-project-top-ten/)
- [CWE - Common Weakness Enumeration](https://cwe.mitre.org/)
- [Linux Privilege Escalation Guide](https://gtfobins.github.io/)
- [JWT Security Best Practices](https://tools.ietf.org/html/rfc8725)

---

## ENGLISH

### 📋 General Description

**SECorNOTsec** is a medium-level offensive security laboratory hosted on Dockerlabs that presents a realistic chain of vulnerabilities. The objective is to demonstrate how multiple security flaws, although not individually critical, can be chained together to achieve total system compromise.

### 🎯 Laboratory Objective

Obtain administrator access and, subsequently, root access to the target system by exploiting six correlated vulnerabilities:

1. **Insecure secrets management** - Backup file exposure
2. **Weak cryptography** - Static Initialization Vector
3. **Session forgery** - JWT cookie forging
4. **Command injection** - RCE with WAF bypass
5. **Weak access control** - Excessive sudo permissions
6. **Library deprivation vulnerabilities** - LD_PRELOAD exploitation

### 🔍 Key Vulnerabilities

| Vulnerability | CVSS | Type | Impact |
|---|---|---|---|
| env.bak exposure | 7.5 | CWE-541 | Secret disclosure |
| Static IV | 5.3 | CWE-327 | Weakened encryption |
| Command Injection | 9.8 | CWE-78 | Remote code execution |
| Sudo misconfiguration | 8.8 | CWE-276 | Privilege escalation |
| LD_PRELOAD abuse | 9.0 | CWE-94 | Escalation to root |

### 📦 Requirements

- **Environment:** Docker / Dockerlabs
- **Recommended Tools:**
  - nmap (port reconnaissance)
  - gobuster (directory fuzzing)
  - Python 3.x (cookie scripting)
  - curl / Burp Suite (web inspection)
  - chmod, sudo, find (system commands)
  
- **Prior Knowledge:**
  - Fundamentals of TCP/IP networking
  - Basic Linux commands
  - Authentication and session concepts
  - Basic cryptography principles

### 🚀 Getting Started

1. **Identify the target:**
   ```bash
   nmap -p- <target_ip>
   ```

2. **Quick analysis:**
   - Check [QuickStart.md](QuickStart.md) for critical steps
   
3. **Detailed analysis:**
   - Check [SECorNOTsecEn.md](SECorNOTsecEn.md) for complete writeup

### 📚 Documentation

- **[SECorNOTsec.md](SECorNOTsec.md)** - Complete technical analysis in Spanish (with screenshots)
- **[SECorNOTsecEn.md](SECorNOTsecEn.md)** - Análisis técnico completo en inglés (con imágenes)
- **[QuickStart.md](QuickStart.md)** - Bilingual quick guide
- **`img/`** - Directory with numbered screenshots

### 💡 Concepts Learned

After completing this laboratory, you will understand:

✅ Secure secrets and configuration management  
✅ Impact of weak cryptographic parameters  
✅ Exploitation of cookies without robust validation  
✅ WAF bypass and obfuscation techniques  
✅ Principle of least privilege in sudoers  
✅ LD_PRELOAD vulnerabilities and mitigation  
✅ Privilege escalation chains  

### 🏆 Estimated Difficulty

- **Reconnaissance:** ⭐ (Easy)
- **Command injection:** ⭐⭐ (Medium)
- **Sudo escalation:** ⭐⭐⭐ (Hard)
- **LD_PRELOAD:** ⭐⭐ (Medium)
- **Total time:** 20-40 minutes

### ⚠️ Legal Notes

This laboratory is designed **exclusively for educational purposes** in authorized environments. Unauthorized access to computer systems is illegal.

### 📞 Additional Resources

- [OWASP Top 10 Vulnerabilities](https://owasp.org/www-project-top-ten/)
- [CWE - Common Weakness Enumeration](https://cwe.mitre.org/)
- [Linux Privilege Escalation Guide](https://gtfobins.github.io/)
- [JWT Security Best Practices](https://tools.ietf.org/html/rfc8725)

---

**Last Updated:** April 2026 | **Status:** Complete Writeup + Mitigation Recommendations
