# Pequeñas Mentirosas

![alt text](img/icon.png)

## Descripción General

Este ha sido un CTF distinto a otros, donde se ponen a prueba nuevas habilidades como ataques por fuerza bruta con Hydra e identificación de archivos vulnerables en ubicaciones críticas. Aunque haya sido un CTF de nivel fácil, no dejó de ser un gran reto que permitió aprender y desarrollar habilidades nuevas. Su organización curiosa y sus distintos distractores lo hicieron idóneo para comprender la importancia de priorizar la información y los procesos.

## Fase 1: Reconocimiento con Nmap

Se comenzó el laboratorio con un análisis de Nmap, realizado para verificar todos los puertos, utilizando el flag `-T4` para agilizar el proceso (no recomendable en entornos de producción).

![alt text](img/NmapScan.png)

Posteriormente se identificaron dos puertos abiertos en el laboratorio: el **22** (SSH) y el **80** (HTTP).

## Fase 2: Enumeración del Servicio HTTP

Se revisó el contenido del puerto 80, visualizando lo siguiente:

![alt text](img/httpService.png)

El único contenido visible fue un texto que decía: "Pista: Encuentra la clave para A en los archivos". Esto permitió analizar que en cierto punto del laboratorio se presentarían documentos con credenciales. Se realizó un análisis del servicio HTTP con `gobuster` en busca de documentos, sin embargo no se encontró nada relevante.

## Fase 3: Ataque de Fuerza Bruta contra Usuario A

Tomando en cuenta la pista nuevamente, se interpretó que "A" probablemente era un usuario. Se ejecutó un ataque por fuerza bruta utilizando Hydra con el diccionario `rockyou.txt`.

![alt text](img/HydraAttackForUserA.png)

Tras el ataque, se obtuvo la contraseña del usuario. Con ambas credenciales, se procedió a ingresar al servicio SSH.

![alt text](img/LogInSshUserA.png)

## Fase 4: Análisis del Usuario A

Después de ingresar se ejecutaron pruebas comunes para determinar los privilegios disponibles:

![alt text](img/WhoAmIUserA.png)

No se encontraron privilegios que permitieran avanzar significativamente. Al ejecutar el comando `find` para seguir la pista inicial, se obtuvieron resultados interesantes:

![alt text](img/FindCommandUserA.png)

Se identificaron varios binarios como `passwd`, `gpsswd` o `sudo`, pero resultaron ser distractores intencionales ya que el usuario "A" no poseía los privilegios necesarios para utilizarlos.

![alt text](img/BinPasswdUserA.png)

## Fase 5: Descubrimiento de Documentos Críticos

Al revisar el directorio raíz del servicio `/srv/ftp`, se encontraron los documentos que permitieron avanzar:

![alt text](img/Srv-ftpUserA.png)

Se identificó una gran cantidad de información, incluyendo archivos encriptados con AES o RSA. Sin embargo, estos resultaban ser distractores adicionales. El documento más relevante fue `pista_de_fuerza_bruta.txt`, el cual mencionaba la necesidad de realizar otro ataque de fuerza bruta para obtener las credenciales de otro usuario llamado "spencer".

![alt text](img/CatBruteForceAdviceDocument.png)

## Fase 6: Ataque de Fuerza Bruta contra Usuario Spencer

En este punto existían dos enfoques posibles:

1. Realizar otro ataque con Hydra utilizando el diccionario `rockyou.txt`
2. Utilizar el documento `hash_spencer.txt` con una herramienta de desencriptación

Ambos métodos eran funcionales. Se procedió con Hydra:

![alt text](img/HydraAttacUserSpencer.png)

Se obtuvieron las credenciales del usuario Spencer y se inició sesión en SSH con este usuario.

![alt text](img/LogInSshUserSpencer.png)

## Fase 7: Escalada de Privilegios

Al ejecutar `sudo -l` (comando fundamental en auditorías para escalada de privilegios), se obtuvo:

![alt text](img/SudoCommandsUserSpencer.png)

Este usuario poseía permisos para ejecutar `python3` sin contraseña. Se procedió a explotar esta vulnerabilidad:

![alt text](img/Root.png)

## Conclusión

**¡Acceso a root obtenido exitosamente!**