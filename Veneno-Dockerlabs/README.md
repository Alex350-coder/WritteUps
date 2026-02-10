# Análisis de Vulnerabilidad: Veneno  LFI y Log Poisoning en Apache (DockerLabs)

**Descripción:** Laboratorio educativo y análisis técnico que demuestra una cadena de explotación basada en LFI y envenenamiento de logs contra un servicio Apache en Docker, con escalada de privilegios hasta `root`.

 **Inicio Rápido:** Ver `quickstart.md` para los pasos compactos de reproducción (escaneo, descubrimiento, verificación LFI, log poisoning y reverse shell).

**Resumen rápido (inline):**
1. `nmap`  2. `gobuster`  3. `ffuf` sobre `problems.php`  4. Leer logs (`/var/log/apache2/error.log`)  5. Inyectar PHP en logs y disparar reverse shell  6. Enumeración y escalada via credenciales/metadatos.

 **Documentación Detallada:**
- `Veneno.md` - Informe técnico completo en español (mejorado).
- `VenenoEn.md` - Complete technical report in English.
- `quickstart.md` - Guía ultra-concisa para replicación rápida (ES / EN).

**Estructura del proyecto:**
- `Veneno.md`
- `VenenoEn.md`
- `quickstart.md`
- `img/` (capturas usadas en el informe)

**Hallazgos y mitigación (resumen):**
- Hallazgo: LFI en `problems.php` que permite incluir logs y ejecutar código  Impacto: Compromiso total del sistema. (Ver `Veneno.md#conclusión`).
- Hallazgo: Información sensible en metadatos de imágenes  Mitigación: eliminar EXIF en cargas y escanear uploads. (Ver `Veneno.md#mitigación--recomendaciones-de-corrección`).

**Licencia / Uso:** Proyecto de laboratorio y aprendizaje. No ejecutar en entornos de producción sin autorización.
