# 🎮 WarGames - Docker Labs Penetration Testing

> **Writeup profesional del laboratorio WarGames | Professional writeup of WarGames Lab**

---

## 📖 Descripción / Description

Writeup completo y profesional para el laboratorio **WarGames de Docker Labs**, incluyendo técnicas de penetration testing, análisis de vulnerabilidades y recomendaciones de seguridad.

Complete and professional writeup for the **WarGames lab from Docker Labs**, including penetration testing techniques, vulnerability analysis, and security recommendations.

---

## 🎯 Vulnerabilidades Cubiertas / Covered Vulnerabilities

| # | Vulnerabilidad | Descripción | Impacto |
|---|---|---|---|
| 1 | **Prompt Injection** | Manipulación de sistemas basados en IA | 🔴 **Crítico** |
| 2 | **SUID Binaries Inseguros** | Binarios con permisos elevados sin validación | 🔴 **Crítico** |
| 3 | **Weak Password Hashing** | SHA-256 sin salt ni protección adicional | 🟠 **Alto** |

---

## 📚 Documentos Disponibles / Available Documents

### Español 🇪🇸

```
📄 Wargames.md
├─ Descripción General
├─ Reconocimiento Inicial (Ping, Nmap)
├─ Enumeración HTTP (Gobuster, README.txt)
├─ Descubrimiento Prompt Injection (Netcat, Payloads)
├─ Extracción de Credenciales (Hash SHA-256)
├─ Escalada de Privilegios (SUID, Ghidra Analysis)
└─ Recomendaciones Profesionales de Seguridad
```

### English 🇬🇧

```
📄 Wargames_EN.md
├─ Overview
├─ Initial Reconnaissance (Ping, Nmap)
├─ HTTP Enumeration (Gobuster, README.txt)
├─ Prompt Injection Discovery (Netcat, Payloads)
├─ Credential Extraction (SHA-256 Hash)
├─ Privilege Escalation (SUID, Ghidra Analysis)
└─ Professional Security Recommendations
```

---

## 🔧 Herramientas Utilizadas / Tools Used

```
┌─ Reconocimiento / Reconnaissance
│  ├─ ping
│  ├─ nmap
│  └─ gobuster
│
├─ Enumeración / Enumeration
│  ├─ netcat (nc)
│  └─ curl/wget
│
├─ Explotación / Exploitation
│  ├─ hash-identifier
│  ├─ Hashes.com/ pagina web de desencriptacion
│  └─ SSH
│
├─ Análisis Binario / Binary Analysis
│  └─ Ghidra
│
└─ Escalada de Privilegios / Privilege Escalation
   └─ find command (SUID enumeration)
```

---

## 📊 Fases del Ataque / Attack Phases

### Fase 1: Reconocimiento | Phase 1: Reconnaissance
- ✅ Verificación de conectividad
- ✅ Escaneo de puertos
- ✅ Identificación de servicios

### Fase 2: Enumeración | Phase 2: Enumeration
- ✅ Enumeración HTTP
- ✅ Fuzzing de directorios
- ✅ Descubrimiento de README.txt

### Fase 3: Explotación | Phase 3: Exploitation
- ✅ Descubrimiento de interfaz de netcat
- ✅ Prompt Injection en chatbot
- ✅ Obtención de credenciales

### Fase 4: Post-Explotación | Phase 4: Post-Exploitation
- ✅ Acceso SSH
- ✅ Búsqueda de binarios SUID
- ✅ Análisis binario con Ghidra
- ✅ Obtención de privilegios root

---

## 🛡️ Recomendaciones Clave / Key Recommendations

### 🔐 Prompt Injection Protection
- ✓ Validación rigurosa de entrada
- ✓ Prompt engineering defensivo
- ✓ Sandboxing y limitación de funciones
- ✓ Monitoreo de comportamiento anómalo

### 🔒 SUID Binaries Security
- ✓ Nombres genéricos para binarios sensibles
- ✓ Auditoría regular de permisos
- ✓ Validación estricta de parámetros
- ✓ Análisis de código estático

### 🔑 Password Hash Protection
- ✓ Algoritmos modernos (bcrypt, argon2, scrypt)
- ✓ Implementación de salt único
- ✓ Factor de trabajo elevado
- ✓ TLS 1.2+ obligatorio en transmisión

---
**⭐ Si este writeup te fue útil, ¡por favor dale una estrella! / If this writeup was helpful, please star it! ⭐**

```
╔════════════════════════════════════════╗
║      WarGames - Docker Labs Lab       ║
║     Happy Hacking and Learning! 🎯    ║
╚════════════════════════════════════════╝
```

---

*Última actualización / Last update: Enero 2026 | January 2026*
