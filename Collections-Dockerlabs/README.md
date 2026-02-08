# Collections - Dockerlabs CTF WriteUp

## 📋 Descripción

**Collections** es un desafío Capture The Flag (CTF) de nivel medio alojado en Dockerlabs. El objetivo consiste en escalar privilegios desde un usuario regular hasta acceso de administrador (root) en un entorno que ejecuta WordPress con extensiones vulnerables.

## 🎯 Objetivo Principal

Obtener acceso de **root** mediante la explotación de vulnerabilidades en WordPress, específicamente a través de la extensión **Hello Dolly** que permite ejecución remota de código (RCE).

## 📁 Contenido del Repositorio

| Archivo | Descripción |
|---------|------------|
| **Collections.md** | Análisis detallado del CTF en español con explicaciones paso a paso |
| **CollectionsEn.md** | Versión en inglés del análisis detallado |
| **QuickStart.md** | Guía rápida con comandos específicos para resolver el CTF |
| **README.md** | Este archivo - descripción general |
| **img/** | Capturas de pantalla del proceso de explotación |

## 🔑 Información Técnica

### Puertos Identificados
- **Puerto 22**: SSH
- **Puerto 80**: HTTP (WordPress)
- **Puerto 27017**: MongoDB

### Vulnerabilidades Explotadas
1. **Credenciales débiles** en WordPress
2. **RCE en Hello Dolly** (extensión de WordPress)
3. **Reutilización de contraseñas** entre usuarios
4. **Información sensible** en archivos de configuración

### Flujo de Escalada de Privilegios
```
Acceso anónimo → Usuario chocolate (SSH) → Usuario dbadmin (administrador DB) → root
```

## 🚀 Cómo Usar

### Opción 1: Guía Completa
Lee **Collections.md** para un análisis detallado con capturas de pantalla y explicaciones profundas de cada fase.

### Opción 2: Resolución Rápida
Sigue **QuickStart.md** para obtener comandos específicos y resolver el CTF de manera ágil.

### Opción 3: Versión en Inglés
Consulta **CollectionsEn.md** para la misma información en idioma inglés.

## 💡 Conceptos Clave Aprendidos

- **Enumeración web** con Nmap y Gobuster
- **Análisis de aplicaciones WordPress** con WPScan
- **Explotación de extensiones vulnerable**
- **Inyección de código** PHP en plugins
- **Reverse shells** y acceso remoto
- **Escalada horizontal y vertical** de privilegios
- **Extracción de credenciales** de archivos de configuración

## 🛠️ Herramientas Utilizadas

```bash
nmap          # Escaneo de puertos
gobuster      # Fuzzing web
wpscan        # Análisis de WordPress
hydra         # Ataque de fuerza bruta
netcat        # Listener para reverse shell
ssh           # Acceso remoto seguro
```

## ⚡ Dificultad

- **Nivel**: Medio
- **Tiempo estimado**: 45-60 minutos
- **Requisitos**: Conocimiento básico de seguridad ofensiva

## 📝 Notas Importantes

- Los archivos de configuración (wp-config.php) con credenciales son críticos
- La reutilización de contraseñas es la clave para escalada final
- El análisis manual del sitio es más efectivo que herramientas automatizadas en este caso
- Las extensiones de WordPress pueden ser puntos de entrada críticos

## 🔗 Referencias

Para más información sobre los temas cubiertos:
- [OWASP WordPress](https://owasp.org/www-community/attacks/WordPress)
- [CWE-434: Unrestricted Upload of File](https://cwe.mitre.org/data/definitions/434.html)
- [Pentestmonkey RCE Payloads](https://pentestmonkey.net/)

---

**Autor**: WriteUp técnico de Collections CTF  
**Plataforma**: Dockerlabs  
**Nivel**: Medio  
**Última actualización**: 2026
