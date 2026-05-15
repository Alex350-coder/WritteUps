# Dance Samba - Laboratorio de Penetración Dockerlabs

## 📋 Descripción Ejecutiva

**Dance Samba** es un laboratorio de penetración de nivel medio diseñado para enseñar las implicaciones de seguridad derivadas del encadenamiento de múltiples vulnerabilidades de configuración en sistemas Linux. A través de una secuencia realista de explotación, este laboratorio demuestra cómo fallos aparentemente menores pueden converger para comprometer completamente la seguridad de un servidor.

### Objetivos de Aprendizaje
- Entender la importancia de la enumeración sistemática de servicios
- Explotar configuraciones deficientes en servicios de red (FTP, SMB)
- Aplicar técnicas avanzadas de evasión de autenticación
- Identificar y explotar almacenamiento inseguro de credenciales
- Realizar escalada de privilegios mediante abuso de configuración sudoers
- Reconocer cómo el encadenamiento de vulnerabilidades amplifica el riesgo

---

## 🎯 Información del Laboratorio

| Aspecto | Detalle |
|--------|---------|
| **Nivel de Dificultad** | Medio |
| **Plataforma** | Dockerlabs |
| **Sistema Operativo** | Linux (Ubuntu) |
| **Tipo** | Encadenamiento de vulnerabilidades |
| **Tiempo Estimado** | 30-60 minutos (primera vez) |
| **Requisitos Previos** | Conocimientos básicos de penetración, Linux, networking |

---

## 🔧 Requisitos

### Herramientas Necesarias
- **nmap**: Escaneo de puertos
- **smbclient**: Conexión y enumeración SMB
- **ftp**: Acceso a servicio FTP
- **ssh**: Conexión remota segura
- **ssh-keygen**: Generación de pares de claves
- Herramientas estándar de terminal: `base32`, `base64`, `echo`, `sudo`

### Requisitos del Sistema
- Acceso a una máquina Linux (Kali, Parrot, o similar)
- Docker instalado y ejecutándose
- Conectividad de red hacia el contenedor del laboratorio

---

## 📚 Documentación Incluida

### Archivos del Proyecto

1. **DanceSamba.md** 📄
   - Análisis detallado paso a paso del laboratorio
   - Explicación técnica profunda de cada fase
   - Imágenes de evidencia para cada etapa
   - Análisis de vulnerabilidades
   - Recomendaciones de mitigación

2. **DanceSambaEN.md** 🌐
   - Versión completa en inglés
   - Traducción profesional manteniendo la jerga técnica
   - Estructura idéntica a la versión en español

3. **QuickStart.md** ⚡
   - Guía rápida con comandos esenciales
   - Pasos críticos para resolución rápida
   - Disponible en español e inglés
   - Ideal para referencias rápidas durante la práctica

4. **README.md** 📖
   - Este archivo
   - Guía general del proyecto
   - Información de requisitos y configuración
---

## 📖 Cómo Usar Esta Documentación

### Para Principiantes
1. Lee el resumen ejecutivo de este README
2. Intenta resolver el laboratorio sin ayuda
3. Consulta **QuickStart.md** si necesitas orientación
4. Lee **DanceSamba.md** para comprender cada paso en profundidad

### Para Usuarios Avanzados
1. Consulta **QuickStart.md** para una referencia rápida
2. Utiliza **DanceSamba.md** como referencia técnica
3. Estudia el análisis de vulnerabilidades y recomendaciones

### Para Propósitos Educativos
1. Cubre los conceptos presentados en **DanceSamba.md**
2. Utiliza como material de enseñanza sobre encadenamiento de vulnerabilidades
3. Asigna a estudiantes y permite que completen el laboratorio
4. Revisa recomendaciones de mitigación para discusión

---

## 🎓 Conceptos Técnicos Cubiertos

### Enumeración y Reconocimiento
- Escaneo de puertos con nmap
- Identificación de versiones de servicios
- Enumeración de recursos compartidos SMB
- Análisis de servicios disponibles

### Explotación de Servicios
- Acceso anónimo a FTP
- Enumeración y acceso a recursos SMB
- Inyección de claves SSH
- Abuso de confianza de servicios

### Escalada de Privilegios
- Decodificación de credenciales
- Análisis de configuración sudoers
- Abuso de binarios ejecutables como root
- Cambio de usuario con credenciales comprometidas

### Seguridad de Información
- Importancia de control de acceso
- Almacenamiento seguro de credenciales
- Principio de menor privilegio
- Auditoría y logging

---

## 🔒 Vulnerabilidades Principales

| Vulnerabilidad | Severidad | Impacto |
|---|---|---|
| Acceso anónimo a FTP | Alto | Divulgación de información |
| SMB mal configurado | Crítica | Evasión de autenticación SSH |
| Credenciales almacenadas en texto | Crítica | Acceso no autorizado |
| Sudoers excesivo | Crítica | Escalada total de privilegios |

---

## ✅ Indicadores de Éxito

- [ ] Acceso anónimo a FTP obtenido
- [ ] Primera bandera (user.txt) capturada
- [ ] Acceso SSH sin contraseña logrado
- [ ] Credencial de root decodificada
- [ ] Acceso root obtenido
- [ ] Bandera final capturada

---

## 💡 Consejos y Trucos

### Debugging
- Si SSH no funciona, verifica que los permisos de `.ssh` sean correctos
- Si SMB no permite escritura, comprueba la configuración de Samba
- Si la decodificación no funciona, verifica el orden: base32 primero, luego base64

### Optimización
- Realiza el escaneo nmap completo al inicio
- Descarga todos los archivos FTP accesibles
- Enumera todos los shares SMB disponibles
- Documenta credenciales conforme las encuentres

### Troubleshooting
```bash
# Verificar conectividad
ping <target_ip>

# Verificar puertos abiertos
nmap -sV <target_ip>

# Listar shares SMB
smbclient -L //<target_ip>/ -N

# Conectar a SMB sin contraseña
smbclient //<target_ip>/share -N
```

---

## 📚 Recursos Adicionales

### Documentación de Herramientas
- [Nmap Documentation](https://nmap.org/docs.html)
- [Samba Client Man Page](https://linux.die.net/man/1/smbclient)
- [SSH Key Generation](https://man7.org/linux/man-pages/man1/ssh-keygen.1.html)
- [Sudo Man Page](https://linux.die.net/man/8/sudo)

### Conceptos de Seguridad
- CVSS v3.1 Scoring
- Encadenamiento de vulnerabilidades
- Escalada de privilegios
- Auditoría de seguridad

---

## 🔍 Preguntas de Reflexión

Después de completar este laboratorio, considera:
1. ¿Cuál fue la vulnerabilidad más crítica?
2. ¿En qué punto se pudo haber prevenido la explotación?
3. ¿Cómo implementarías controles para cada vulnerabilidad?
4. ¿Qué monitoreo detectaría estas actividades?

---

## 📋 Estructura de Archivos

```
DanceSamba-Dockerlabs/
├── DanceSamba.md           # Análisis técnico completo (Español)
├── DanceSambaEN.md         # Análisis técnico completo (Inglés)
├── QuickStart.md           # Guía rápida bilingüe
├── README.md               # Este archivo
└── img/
    ├── 1-nmap.png
    ├── 2-FtpAnonymous.png
    ├── 3-Macarena.png
    ├── 4-Nota.png
    ├── 5-SMBMacarena.png
    ├── 5-FristFlag.png
    ├── 6-RSA.png
    ├── 7-SSHmacarena.png
    ├── 8-CatETCPasswd.png
    ├── 8-find.png
    ├── 9-hash.png
    ├── 10-hashDecrypt.png
    ├── 11-sudo-L.png
    ├── 11-password.png
    ├── 12-fileToRead.png
    └── root.png
```

---

## 📝 Notas Finales

### Para Instructores
Este laboratorio es ideal para enseñar:
- La importancia de la enumeración sistemática
- Cómo múltiples fallos pequeños pueden crear vulnerabilidades críticas
- La necesidad de hardening de sistemas
- Importancia del seguimiento del principio de menor privilegio

### Para Estudiantes
- Completa este laboratorio de forma independiente antes de consultar documentación
- Documenta tus pasos y descubrimientos
- Compara tu enfoque con el de la documentación
- Intenta encontrar rutas alternativas de explotación

### Para Profesionales
- Utiliza como referencia de exploración de vulnerabilidades
- Adapta técnicas a auditorías reales (con permiso)
- Considera cómo se podrían detectar estos ataques
- Desarrolla playbooks de respuesta a incidentes basados en el escenario

---

## 📞 Soporte

Para problemas con:
- **Herramientas**: Consulta la documentación oficial
- **Conceptos técnicos**: Lee la sección relevante en DanceSamba.md
- **Comandos específicos**: Revisa QuickStart.md
- **Configuración de Docker**: Verifica la documentación de Dockerlabs

---

**Versión**: 1.0  
**Última actualización**: 2026  
**Licencia**: Fines educativos  
**Autor**: Equipo de Ciberseguridad

---

## 🎯 Próximos Pasos

Después de completar este laboratorio:
1. Explora laboratorios Dockerlabs más avanzados
2. Estudia técnicas de detección y respuesta a incidentes
3. Practica defensa de sistemas Linux
4. Desarrolla competencias en auditoría de seguridad
5. Participa en competiciones CTF
