### Informe de maquina *"Hedgehog"*

![maquina](./screenshots/01_maquina.png)

Empezamos nuestra maquina primero dadon permisos de ejecución al archivo `auto_deploy.sh` con `sudo chmod +x`

![permisos a auto_deploy.sh](./screenshots/02_permisos_sh.png)

Inicializamos nuestro laboratorio con: `sudo ./auto_deply.sh hedgehog.tar`

![inicialización máquina](./screenshots/03_inicio_maquina.png)

Realizamos un escaneo de puertos con la herramienta `nmap`

![Escaneo de puertos](./screenshots/04_nmap_scan1.png)

Se identifican abiertos los puertos `22` y `80`, se procede a buscar mas detalle de los mismos con la herramienta `nmap`

![Detalle puerto 22 y 80](./screenshots/05_nmap_scan2.png)

Visualizamos que nos aparece en el navegador, solo encontramos un texto con `tails`

![Página web](./screenshots/06_page.png)
![Código fuente página web](./screenshots/07_page2.png)

Realizamos un fuzzing de la página en busqueda de otros archivos o directorios.

![Fuzzing de página web](./screenshots/08_fuzzing.png)

Siguiendo la documentación de otros usuarios nos sugieren realizar un reverse del diccionario rockyou para ahorrar tiempo en encontrar la contraseña de `tails`.

![Reverse de diccionario rockyou](./screenshots/09_reverse_dic.png)

![Fuerza bruta contraseña](./screenshots/10_passwd.png)

Se encentra que en efecto existe el usuario `tails` y este posee la contraseña `3117548331`

![Acceso a servidor](./screenshots/13_check_permisions.png)

Realizamos una busqeda de permisos sudo con `sudo -l`, encontramos que el usuario "sonic" posee ejecución de altos privilegio sin contraseña

![Busqueda de otros usuarios](./screenshots/14_check_users.png).

Intentamos logearnos como el usuario "sonic" con el comando `sudo -u sonic bash` una vez como sonic nos podremos loguear como usuario `root`

![Logeo como sonic](./screenshots/15_root.png)
