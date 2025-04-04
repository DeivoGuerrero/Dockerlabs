### Informe de máquina *"CosoleLog"*

![Máquina ConsoleLog](./screenshots/01_machine.png)

Vamos a realizar la maquina "CosoleLog", como siempre vamos a empezar asignando permisos de ejecución al archivo `auto_deploy.sh` eh inicializamos la maquina con `sudo ./autodeploy ` `consolelog.tar`

![Permisos ejecución deploy](./screenshots/02_execution_perms_deploy.png)
![Inicialización máquina](./screenshots/03_run_machine.png)

Realizamos un testeo de conexión con el comando `ping`.

![Test de conexión](./screenshots/04_ping.png)

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

![Escaneo nmap](./screenshots/05_nmap.png)

Encontramos los puertos `80`, `3000` y `5000` abiertos, procedemos a realizar un escaneo más detallada de estos.

```bash
nmap -p80,3000,5000 -sC -sV -O 172.17.0.2
```

- `nmap` → Ejecuta la herramienta de escaneo de red Nmap.

- `-p80,3000,5000` → Especifica los puertos que queremos escanear. En este caso 80, 3000 y 5000.

- `-sC` → Ejecuta scripts de detección predeterminados

- `-sV` → Detección de versiones de servicios.

- `-O` → Detección del sistema operativo.

- `172.17.0.2` → Especifica la dirección IP del objetivo a escanear.

![Detalle puertos 80, 3000 y 5000](./screenshots/06_details_ports_80_3000_5000.png)

Continuamos a revisar que encontramos en la página web de inicio.

![Página web](./screenshots/07_web_page.png)

Encontramos una página con un botón que dice: "Boton en fase beta", revisemos el código fuente.

![Código fuente](./screenshots/08_source_code_web_page.png)

Observamos que hay un Script con dirección a `authentication.js`, revisemos el contenido del mismo.

![Función authentication](./screenshots/09_func_autenticate.png)

En esta función encontramos un console.log que dice que el token del /recurso/ es `tokentraviesito`

Se decide realizar un ataque de Fuzzing a servidor web en busqueda de otros archivos o directorios.

![Fuzzing a servidor web](./screenshots/10_fuzzing_web_server.png)

Encontramos un directorio llamado "backend".

![Directorio backend](./screenshots/11_backend_directory.png)

Nos llama la atención el archivo server.js.
Abriendo el contenido del mismo encontramos una función, que obteniendo el token identificado anteriormente retornará una página con el texto: `lapassworddebackupmaschingonadetodas`

![Archivo server.js](./screenshots/12_server_js.png)

Asumimos que es la contraseña de algun usuario, realizamos un ataque de fuerza bruta con la herramienta `hydra` seteando el puerto 5000

```bash
hydra -L /usr/share/wordlists/john.lst -p "lapassworddebackupmaschingonadetodas" -s 5000 ssh://172.17.0.2 -t 10
```

![Fuerza bruta usuario](./screenshots/13_find_user_lovely.png)

Como resultado encontramos al usuario `lovely`, accedemos con el mismo y revisamos los permisos sudo de ejecución.

![Logueo como usuario lovely](./screenshots/14_login_as_lovely.png)

Encontramos que puede ejecutar con permisos `sudo` el comando `nano` procedemos a buscar como ejecutar una shell con este comando en [GTFOBins](https://gtfobins.github.io/)

![Busqueda shell con nano](./screenshots/15_find_shell_nano.png)

1. Ejecutamos `sudo nano`
2. Una vez en el editor presionamos `Ctrl + R`
3. Luego presionamos `Ctrl + X`
4. Nos pedira que ejecutemos un comando, escribimos `reset; sh 1>&0 2>&0` y damos Enter.
5. Ejecutamos comando `whoami` para comprobar que somos usuarios `root`

![Acceso como usuario root](./screenshots/16_login_as_root.png)
