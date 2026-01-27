# Apolo - CTF DockerLabs

## 📋 Descripción

**Apolo** es un Capture The Flag (CTF) de nivel medio disponible en [DockerLabs](https://dockerlabs.es), especializado en vulnerabilidades comunes en aplicaciones web y escalación de privilegios en sistemas Linux.

En este laboratorio se exploran múltiples técnicas de pentesting incluyendo:
- Reconocimiento y enumeración
- Inyección SQL
- Carga y ejecución de código malicioso
- Reverse shells
- Escalación de privilegios

---

## 📁 Contenido del repositorio

- **Apolo.md** - Documentación completa del laboratorio en español con análisis detallado, capturas de pantalla y explicaciones paso a paso
- **ApoloEn.md** - Versión en inglés de la documentación completa
- **QuickStart.md** - Guía rápida para resolver el laboratorio sin explicaciones extensas
- **img/** - Directorio con todas las imágenes utilizadas en la documentación

---

## 🚀 Inicio rápido

Para una resolución rápida del laboratorio, consulte [QuickStart.md](QuickStart.md).

Para un análisis detallado y completo con explicaciones, consulte [Apolo.md](Apolo.md).

---

## 🎯 Vulnerabilidades principales

### 1. **SQL Injection (Union-based)**
Vulnerabilidad en el formulario de búsqueda que permite extraer datos de la base de datos.

### 2. **File Upload vulnerabilidad**
Carga de archivos sin validación adecuada de tipo de archivo, permitiendo bypass con extensiones alternativas.

### 3. **Remote Code Execution (RCE)**
Ejecución remota de código a través de shell web subida.

### 4. **Contraseñas débiles**
Uso de hashing débil (MD5/SHA1) en lugar de algoritmos modernos como bcrypt o Argon2.

### 5. **Pertenencia a grupos privilegiados**
Acceso al archivo `/etc/shadow` debido a membresía en grupo shadow.

---

## 📚 Herramientas utilizadas

- **nmap** - Escaneo de puertos
- **gobuster** - Enumeración de directorios web
- **sqlmap** - Detección y explotación de SQL injection
- **BurpSuite** - Interceptación y modificación de peticiones HTTP
- **john the ripper** - Descifrado de hashes de contraseña
- **netcat (nc)** - Reverse shell

---

## 💡 Recomendaciones de seguridad

Cada vulnerabilidad documentada incluye 3 recomendaciones detalladas de mitigación:

- Usar prepared statements para prevenir SQL injection
- Validar tipos de archivo en servidor
- Ejecutar aplicaciones con mínimos privilegios
- Implementar hashing moderno con salt
- Auditar membresías de grupos del sistema

Para detalles completos, consulte la sección "Resumen de vulnerabilidades encontradas" en [Apolo.md](Apolo.md).

---

## 📝 Estructura de aprendizaje

Se recomienda seguir el laboratorio en este orden:

1. **Lectura inicial:** Comience con [Apolo.md](Apolo.md) para entender la metodología
2. **Práctica:** Use [QuickStart.md](QuickStart.md) como referencia mientras resuelve el laboratorio
3. **Referencia:** Vuelva a [Apolo.md](Apolo.md) para profundizar en vulnerabilidades y recomendaciones

---

## 🔍 Requisitos

- Entorno DockerLabs o similar
- Herramientas de pentesting estándar (nmap, gobuster, sqlmap, BurpSuite)
- Diccionarios de palabras (rockyou.txt)
- Conocimientos básicos de:
  - HTTP y aplicaciones web
  - SQL
  - Linux y comandos de terminal
  - Hashing de contraseñas

---

## 🔗 Enlaces útiles

- [DockerLabs](https://dockerlabs.es) - Plataforma oficial de laboratorios
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) - Vulnerabilidades web más comunes
- [HackTricks](https://book.hacktricks.xyz) - Guía de técnicas de hacking

---

**¡Gracias por revisar este CTF!**
