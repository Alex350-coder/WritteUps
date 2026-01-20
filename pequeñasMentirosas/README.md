# Pequeñas Mentirosas - WriteUp CTF

## Tabla de Contenidos

1. [Descripción General](#descripción-general)
2. [Requisitos](#requisitos)
3. [Fases del Laboratorio](#fases-del-laboratorio)
4. [Documentación Disponible](#documentación-disponible)

## Descripción General

**Pequeñas Mentirosas** es un CTF de nivel fácil que desafía a los participantes a desarrollar habilidades en múltiples áreas de seguridad ofensiva. El laboratorio combina reconocimiento de servicios, ataques de fuerza bruta y escalada de privilegios para lograr acceso de administrador.

### Concepto

El nombre del CTF hace referencia a los múltiples distractores intencionales encontrados durante la explotación, dando lugar a "pequeñas mentiras" que desvían la atención de la solución real.

## Requisitos

- Acceso a máquina Linux con herramientas estándar de penetración
- Conocimiento básico de SSH, HTTP y auditoría de sistemas
- Herramientas necesarias:
  - `nmap` - Escaneo de puertos
  - `hydra` - Ataque de fuerza bruta
  - `gobuster` - Enumeración de directorios web
  - `python3` - Ejecución de scripts

## Fases del Laboratorio

El proceso de explotación se divide en 7 fases principales:

1. **Reconocimiento** - Identificación de servicios activos
2. **Enumeración** - Búsqueda de puntos de entrada
3. **Acceso Inicial** - Explotación del usuario A
4. **Investigación** - Análisis de privilegios y archivos
5. **Lateral Movement** - Obtención de credenciales de Spencer
6. **Análisis de Permisos** - Identificación de vulnerabilidades sudo
7. **Escalada Privilegios** - Obtención de acceso root

## Documentación Disponible

### Análisis Detallado en Español

Para una explicación completa paso a paso con capturas de pantalla, consulte [Pequeñas Mentirosas](PuequeñasMentirosas.md).

### Análisis Detallado en Inglés

For a detailed explanation in English, see [Little Liars](PuequeñasMentirosasEn.md).

### Guía Rápida

Para un resumen sin detalles y comandos directos, consulte [Quick Start](QuickStart.md).

---

*Este writeup fue desarrollado como parte de un análisis de seguridad y aprendizaje en ciberseguridad.*
