### Informe de maquina *"FirstHacking"*

Empezamos asignando permisos de ejecución a `auto_deploy.sh` con `sudo chmod +x`

![permisos ejecución](./screenshots/01_permisos_deploy.png)

Inicializamos el laboratorio `sudo ./auto_deploy.sh` *nombre maquina*

![inicio maquina](./screenshots/02_run_maquina.png)

Realizamos un escaneo de puertos abiertos con el comando `nmap`

![escaneo nmap](./screenshots/02_nmap_scan1.png)

Se identifíca solo el puerto 21 abierto correspondiente a un servicio ftp.

![escaneo puerto 21](./screenshots/04_scan_p21.png)

Se identifica que el servicio ftp tiene una versión `vsftpd 2.3.4`

![msfconsole](./screenshots/05_msfconsole.png)

Realizamos una busqueda de vulnerabilidades a esa versión del servicio.

![buscar vulnerabilidad](./screenshots/05_search_vulnerability.png)

Precisamente encontramos un exploit de Backdoor para esa versión de servicio, lo seleccionamos.

![seleccionar exploit](./screenshots/06_seleccionar%20exploit.png)

Con el comando `show options` podemos observar que configuraciones podemos hacer para iniciar el exploit.

![opciones exploit](./screenshots/06_show_options.png)

Observamos que el puerto esta definido por defecto al puerto 21, por lo tanto no tenemos que configurarlo.

Pero si debemos definir la IP de la victima con el comando `set RHOSTS ip` ip correspondiendo a: `172.17.0.2`

![vulnerabilidad](./screenshots/06_permisos_deploy.png)

Una vez configurado el exploit, lo ejecutamos con el comando `run`.

Ejecutando el comando `whoami` podemos obaservar que nos ha iniciado una terminal de session como `root`

![root](./screenshots/07_backdoor_root.png)

