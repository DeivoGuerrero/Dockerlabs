### Informe de máquina *"WhereIsMyWebShell"*

![Máquina Psycho](./screenshots/01_maquina.png)

Vamos a realizar la maquina "Psycho", como siempre vamos a empezar asignando permisos de ejecución al archivo `auto_deploy.sh` eh inicializamos la maquina con `sudo ./autodeploy ` `psycho.tar`

![Permisos ejecución deploy](./screenshots/02_permisos_ejecucion_deploy.png)
![Inicialización máquina](./screenshots/03_inicializacion_maquina.png)

Realizamos un testeo de conexión con el comando `ping`.
Y realizamos un escaneo de puertos abiertos con la herramienta `nmap`, el comando usado fue:

```bash
nmap -sS --min-rate 5000 -p- -vvv -Pn -n 172.17.0.2 -oG nmap
```

- `nmap` → Comando para ejecutar herramienta nmap.
- `-sS` → Escaneo SYN ("Stealth Scan"). Envía paquetes SYN sin completar la conexión TCP (más rápido y sigiloso que -sT).
- `--min-rate 5000` → Fuerza a nmap a enviar al menos 5000 paquetes por segundo, acelerando el escaneo.
- `-p-` → Escanea todos los 65535 puertos (en lugar de solo los más comunes).
- `-vvv` → Modo muy detallado (muestra más información en pantalla mientras se ejecuta).
- `-Pn` → No realiza detección de host (asume que el host está activo y no envía ping previo).
- `-n` → No resuelve nombres de dominio (evita consultas DNS, lo que acelera el escaneo).
- `172.17.0.2` → IP objetivo a escanear.
- `-oG nmap` → Guarda los resultados en formato "greppable" en un archivo llamado nmap.

![Test de conexión](./screenshots/04_escaneo_puertos_abiertos.png)

![Escaneo detalle puerto 80](./screenshots/05_escaneo_puerto80.png)
![Página web](./screenshots/06_pagina_web.png)
![Código fuente de página web](./screenshots/07_codigo_fuente_pagina.png)
![Ataque de Fuzzing](./screenshots/08_ataque_fuzzing.png)
![Web warning](./screenshots/09_web_warning.png)
![Ejemplo web shell en php](./screenshots/10_ejemplo_codigo_shell_php.png)
![Fuzzing id de web shell](./screenshots/11_id_shell.png)
![Acceso a web shell](./screenshots/12_acceso_shell.png)
![Contenido en tmp](./screenshots/14_ls_tmp.png)
![Lectura secret.txt](./screenshots/15_passwd_root.png)
![Escucha puerto 4433](./screenshots/16_reverse_shell_listening_port.png)
![Comando reverse shell](./screenshots/16_reverse_shell.png)
![Acceso a terminal](./screenshots/17_acceso_terminal.png)
![Logeo como root](./screenshots/18_acceso_root.png)


