# Reporte de Seguridad: Laboratorio RealGob.DL - Análisis Integral de Vulnerabilidades

## Descripción del Proyecto

Este repositorio contiene un análisis técnico completo de un laboratorio de pruebas de penetración (Docker-Dockerlabs) denominado **Report**. El laboratorio simula un entorno web corporativo real con múltiples vulnerabilidades intencionalmente implementadas. El objetivo de este reporte es documentar sistemáticamente cada hallazgo de seguridad, clasificar su criticidad y proporcionar soluciones técnicas para su remediación.

---

## ⚠️ Resumen Ejecutivo de Hallazgos

El análisis identificó **15 hallazgos técnicos significativos**, de los cuales **6 son CRÍTICOS** y el sistema presentan un **riesgo general CRÍTICO**. La cadena de ataque documentada permite el compromiso total del sistema desde acceso anónimo hasta ejecución de código con privilegios de root.

### Tabla de Hallazgos por Criticidad

| Nivel | Cantidad | Hallazgos Principales |
|-------|----------|----------------------|
| 🔴 **CRÍTICA** | 6 | Carga de archivos (RCE), LFI, IDOR, Manipulación financiera, Git expuesto, Configuración de PHP |
| 🟠 **ALTA** | 5 | XSS, Enumeración de usuarios, Estructura DB, Admin sin protección, Acceso SSH |
| 🟡 **MEDIA** | 4 | important.txt, API expuesta, Logs públicos, Archivos de configuración |

---

## 🚀 Inicio Rápido

Para comenzar rápidamente con una replicación del laboratorio, consulte:

**[Ir a QuickStart.md](QuickStart.md)**

Contiene 7 pasos operativos para reproducir la evaluación completa:
1. Escaneo de puertos con Nmap
2. Descubrimiento de directorios (Gobuster)
3. Pruebas de vulnerabilidades web
4. Explotación de carga de archivos
5. Extracción de repositorio Git
6. Acceso SSH por fuerza bruta
7. Escalada de privilegios a root

**Tiempo estimado**: 2-3 horas

---

## 📚 Documentación Completa

### Informe Técnico en Español
**[Report.md](Report.md)** - Informe técnico completo y detallado

Contiene:
- Introducción y metodología de evaluación
- 15 hallazgos técnicos con análisis profundo
- Screenshots y pruebas de concepto
- Sección "Conclusión" con síntesis ejecutiva
- Sección "Recomendaciones y Soluciones" con código remediador
- Cronograma de remediación priorizado

### Complete Technical Report in English
**[ReportEn.md](ReportEn.md)** - Informe técnico traducido al inglés

Equivalent content to Report.md with:
- Complete methodology and findings
- Technical recommendations and solutions
- Remediation schedule
- Executive summary

---

## 🔧 Soluciones Clave - Top 3 Remediaciones Críticas

Estas tres correcciones deben implementarse **inmediatamente**:

### 1. **Bloquear Acceso a Directorio .git** [CRÍTICA]
```nginx
# nginx.conf
location ~ /\.git {
    deny all;
    access_log off;
}
```
**Impacto**: Previene la extracción de código fuente, historial Git y credenciales embebidas.

---

### 2. **Validación Robusta de Carga de Archivos** [CRÍTICA]
```php
<?php
// Validar extensión, MIME y contenido del archivo
$finfo = new finfo(FILEINFO_MIME_TYPE);
$mime = $finfo->file($file['tmp_name']);

// Whitelist de MIME permitidos
if (!in_array($mime, $allowedMimeTypes)) {
    die('MIME type not allowed');
}

// Renombrar archivo para evitar ejecución
$newName = bin2hex(random_bytes(16)) . '.jpg';
?>
```
**Impacto**: Previene RCE (Remote Code Execution) a través de carga de archivos maliciosos.

---

### 3. **Protección contra LFI** [CRÍTICA]
```php
<?php
// Whitelist de páginas permitidas
$allowed = ['principal', 'historia', 'mision'];
$page = $_GET['page'] ?? 'principal';

if (!in_array($page, $allowed)) {
    die('Página no permitida');
}

// Usar ruta absoluta validada
include __DIR__ . '/pages/' . $page . '.php';
?>
```
**Impacto**: Previene lectura arbitraria de archivos del sistema.

---

### Soluciones Completas
Para todas las 12 remediaciones técnicas, consulte las secciones de **Recomendaciones y Soluciones** en:
- [Report.md - Recomendaciones](Report.md#recomendaciones-y-soluciones)
- [ReportEn.md - Recommendations](ReportEn.md#recommendations-and-solutions)

---

## 📁 Estructura del Proyecto

```
Report-Dockerlabs/
├── README.md                    ← Archivo actual
├── QuickStart.md               ← Guía de 7 pasos operativos
├── Report.md                   ← Informe técnico completo (ESPAÑOL)
├── ReportEn.md                 ← Informe técnico completo (ENGLISH)
└── img/                        ← Capturas de pantalla y pruebas
    ├── nmap.png
    ├── gobuster.png
    ├── infoPHP.png
    ├── cargasPHP.png
    ├── RepetidorBurp.png
    ├── LFI-etcPasswd.png
    ├── XSS.png
    ├── IDOR.png
    ├── TestTRansferencia.png
    ├── gitDumperClone.png
    ├── hydraSSH.png
    ├── root.png
    └── [más imágenes...]
```

---

## 🎯 Matriz de Vulnerabilidades

| # | Vulnerabilidad | Criticidad | Componente | Remediación |
|---|----------------|-----------|-----------|-------------|
| 1 | Exposición de info.php | 🔴 CRÍTICA | PHP Config | Eliminar/Bloquear archivo |
| 2 | Bypass de validación MIME | 🔴 CRÍTICA | Upload | Validar con finfo() |
| 3 | Local File Inclusion (LFI) | 🔴 CRÍTICA | Acerca de | Whitelist de páginas |
| 4 | IDOR en perfiles | 🔴 CRÍTICA | Auth | Verificar autorización |
| 5 | Manipulación financiera | 🔴 CRÍTICA | Transacciones | Validar montos/saldo |
| 6 | Repositorio Git expuesto | 🔴 CRÍTICA | .git | Bloquear directorio |
| 7 | XSS reflejado | 🟠 ALTA | Contacto | Sanitizar con htmlspecialchars() |
| 8 | Enumeración de usuarios | 🟠 ALTA | Login | Mensajes genéricos |
| 9 | Estructura DB expuesta | 🟠 ALTA | database.php | Eliminar/Proteger |
| 10 | SSH sin protección | 🟠 ALTA | SSH | Deshabilitar contraseña |
| 11 | important.txt | 🟡 MEDIA | General | Eliminar |
| 12 | API expuesta | 🟡 MEDIA | API | Documentar endpoints |
| 13 | Logs públicos | 🟡 MEDIA | Logs | Bloquear acceso |
| 14 | Config con credenciales | 🟡 MEDIA | Config | Usar variables de entorno |
| 15 | Admin sin rate limit | 🟡 MEDIA | Admin | Implementar protección |

---

## 📊 Cronograma de Implementación

### Fase Inmediata (24 horas)
- ✅ Bloquear .git
- ✅ Eliminar info.php
- ✅ Bloquear acceso a archivos sensibles

### Fase 1 (3 días)
- ✅ Implementar validación de carga de archivos
- ✅ Proteger contra LFI
- ✅ Cambiar contraseña SSH

### Fase 2 (7 días)
- ✅ Implementar autorización en IDOR
- ✅ Validar transacciones financieras
- ✅ Sanitizar entrada XSS
- ✅ Usar variables de entorno para credenciales

---

## 🔐 Principios de Seguridad Aplicados

Este informe enfatiza los siguientes principios OWASP:

1. **Validación de Entrada**: Whitelist de valores permitidos
2. **Sanitización de Salida**: Escapar HTML con htmlspecialchars()
3. **Principio de Mínimo Privilegio**: Verificación de autorización
4. **Defensa en Profundidad**: Múltiples capas de validación
5. **Secretos Fuera del Código**: Variables de entorno para credenciales
6. **Logging Seguro**: Restringir acceso a logs
7. **Actualizaciones**: Mantener frameworks en versiones seguras

---

## 📖 Cómo Usar Este Reporte

### Para Desarrolladores
1. Consulte [QuickStart.md](QuickStart.md) para entender el vector de ataque
2. Revise el código malicioso en [Report.md](Report.md)
3. Implemente las soluciones PHP/nginx proporcionadas

### Para Administradores de Infraestructura
1. Revise el cronograma de remediación
2. Configure .htaccess o nginx según corresponda
3. Verifique los cambios de contraseña

### Para Auditores de Seguridad
1. Use este reporte como plantilla de evaluación
2. Compare hallazgos con estándares OWASP Top 10
3. Verifique que todas las remediaciones se implementen

---

## ✅ Validación y Testing

Después de implementar cada remediación:

```bash
# Test 1: Verificar que .git no es accesible
curl -I http://realgob.dl/.git/config  # Debe retornar 403 o error

# Test 2: Verificar que info.php no existe
curl http://realgob.dl/info.php  # Debe retornar 404 o error

# Test 3: Verificar sanitización XSS
# Intentar payload en formulario: <script>alert('xss')</script>
# Debe mostrar el texto literal, no ejecutar

# Test 4: Verificar IDOR arreglado
# Intente acceder con usuario diferente al ID solicitado
# Debe retornar 403

# Test 5: Verificar validación de monto
# Intente transferencia negativa
# Debe rechazar con error
```

---
---

## 📝 Resumen de Documentos

| Documento | Contenido | Audiencia |
|-----------|----------|-----------|
| **README.md** (este archivo) | Visión general y resumen ejecutivo | Todos |
| **Report.md** | Informe completo en español con soluciones | Técnicos/Desarrolladores |
| **ReportEn.md** | Informe completo en inglés | Audiencia internacional |
| **QuickStart.md** | 7 pasos operativos para replicar | Pentestadores |

---

**Generado**: Febrero 2026  
**Laboratorio**: Report (Docker-Dockerlabs)  
**Riesgo General**: 🔴 CRÍTICO  
**Estado**: Requiere remediación inmediata
