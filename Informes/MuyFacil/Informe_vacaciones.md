### Informe de maquina *"Vacaciones"*

![maquina](../../data/muy_facil/vacaciones/screenshots/01_machine.png)

El día de hoy realizaremos la máquina "Vacaciones", iniciamos nuestra máquina, asignado primero los permisos de ejecución al `archivo auto_deply.sh` ejecutándolo junto con el archivo `.tar` para inicializar la máquina.

![permisos de ejecución](../../data/muy_facil/vacaciones/screenshots/02_permisos_ejecucion_deploy.png)
![inicio maquina](../../data/muy_facil/vacaciones/screenshots/03_iniciamos_maquina.png)

Realizamos un escaneo de puertos con la herramienta `nmap`.

![Escaneo de puertos](../../data/muy_facil/vacaciones/screenshots/04_scan_nmap.png)

Encontramos abiertos los puertos `22` y `80`, realizamos una búsqueda más detallada de estos.

![Detalle puertos 22 y 80](../../data/muy_facil/vacaciones/screenshots/05_scan_p_22-80.png)

Revisamos que podemos encontrar en la página web.

![Visita de sitio web](../../data/muy_facil/vacaciones/screenshots/06_page.png)

Revisando el código fuente de la página web encontramos 2 posibles usuarios "juan" y "camilo"

![Código fuente página web](../../data/muy_facil/vacaciones/screenshots/07_codigo_page.png)

Realizamos una búsqueda de otros archivos o directorios que podamos navegar, no encontramos nada más.

![Fuzzin de página web](../../data/muy_facil/vacaciones/screenshots/08_fuzz_p80.png)

Abrimos MetaSploit en búsqueda de algún exploit para la versión de ssh

![Inicio de msfconsole](../../data/muy_facil/vacaciones/screenshots/09_msfconsole.png)

Intentaremos ejecutar una enumeración de usuarios para comprobar los usuarios "juan" y "camilo" y si encontramos más

![Seleccionar exploit enum users](../../data/muy_facil/vacaciones/screenshots/10_ssh_enmu_users.png)
![Definir host](../../data/muy_facil/vacaciones/screenshots/11_set_host.png)
![Setear WorldList](../../data/muy_facil/vacaciones/screenshots/12_set_worldlist.png)

Al parecer nos bloquea el servidor y no nos permite realizar la enumeración de usuarios.

Intentaremos buscar la contraseña de los usuarios "juan" y "camilo" por fuerza bruta.

![Fuerza bruta psswd juan](../../data/muy_facil/vacaciones/screenshots/13_check_passwd_juan.png)

Al ver que la fuerza bruta del usuario "juan" tarda mucho, se cancela el ataque y se intenta con el usuario "camilo"

![Fuerza bruta passwd camilo](../../data/muy_facil/vacaciones/screenshots/14_found_passwd_camilo.png)

Se ha encontrado la contraseña del usuario `camilo` es `pasword1`

![Acceso a servidor como camilo](../../data/muy_facil/vacaciones/screenshots/15_login_as_camilo.png)

Accedemos como el usuario `camilo` y realizamos un listado de los permisos que posee: encontramos que este usuario no puede ejecutar comando `sudo`, realizamos un listado de binarios que podamos usar con altos privilegios.

![Busqueda de binarios a explotar](../../data/muy_facil/vacaciones/screenshots/16_search_binarys.png)

Se encuentra binario `/usr/bin/chfn`se intenta realizar la ejecución de consola con este binario, pero sin éxito, antes de seguir intentado con este binario u otros, se decide recapitular de la información encontrada hasta el momento.

![Busqueda de otros datos](../../data/muy_facil/vacaciones/screenshots/17_searching_data.png)

Dentro del index.html correspondiente al código fuente de la página recordamos que el comentario habla de un correo en particular.

![Se encuentra correo](../../data/muy_facil/vacaciones/screenshots/18_mail_secreto.png)

En efecto en el servidor dentro del directorio `/var/mail` se encontraba un directorio llamado `camilo` y dentro de este un archivo llamado `correo.txt`, revisando el contenido de este, encontramos una posible contraseña.

![Acceso como juan](../../data/muy_facil/vacaciones/screenshots/19_loging_as_juan.png)

En efecto la contraseña pertenecía al usuario de `juan`, así que accedemos como este usuario y vemos que puede ejecutar el comando `ruby` col altos privilegios.

![Busqueda de shell con ruby](../../data/muy_facil/vacaciones/screenshots/20_search_reverse_shell.png)

Realizamos una búsqueda en [GTFobins](https://gtfobins.github.io/) para ejecutar una shell con el comando `ruby`

Ejecutamos el comando: 

```bash
sudo ruby -e 'exec "/bin/sh"'
```

De esa forma ejecutando `whoami` logramos ver que pudimos acceder como usuario `root`

![Acceso como root](../../data/muy_facil/vacaciones/screenshots/21_root.png)