### Informe de maquina *"FBorazuwarahCTF"*

![](./screenshots/01_machine.png)

Iniciamos nuestra maquina como es costumbre ya asignando los permisos de ejecución al archivo `auto_deploy.sh` y ejecutando este escrit junto con el archivo de la maquina .tar

```bash
sudo auto_deply.sh borazuwarachcft.tar
```

![maquina](./screenshots/02_inicio_maquina.png)

Realizamos un escaneo de puertos con el comando `nmap`

![Escaneo nmap](./screenshots/03_scan_nmap.png)

Se encuentran abiertos los puertos `22` y `80`, realizamos un escaneo más detallado de ambos.

![Escaneo detalle a puertos 22 y 80](./screenshots/04_scanp22-80.png)

Encontramos que el puerto `22` tiene una versión OpenSSH 9.2 y el puerto `80`tiene una versión Apache https 2.4.59
Pero antes de buscar vulnerabilidades, investiguemos un poco que nos trae de inicio su servidor web.

![página web](./screenshots/05_pagina_web.png)
![código fuente página web](./screenshots/06_codigofuente_pagina.png)

Encontramos una imágen de 'Kinder Sorpresa', sin mucha información de  momento.

Procedemos a descargar la imágen con el comando `wget` y la ruta de la imágen.

![descarga de imágen](./screenshots/09_download_image.png)

Revisamos los metadatos de la imagen con el comando `exiftool` y la imagen

![metadatos imágen](./screenshots/10_metadata_imagen.png)

Observamos que en el atributo "Description" encontramos asignado el valor: ---------- User: `borazuwarah` ----------.
Y en el atributo "Title" el valor ---------- Password: ----------

Teniendo en cuenta que `borazuwarah` es un posible usuario, realizamos un intento de acceso con fuerza bruta haciendo uso de la herramienta `hydra`

```bash
hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt ssh://ip -t 10
```
![fuerza bruta a contraseña](./screenshots/11_passwd.png)

En efecto se pudo encontrar la contraseña con fuerza bruta correspondiente a: `123456`

![acceso a servidor](./screenshots/12_access.png)

Verificamos que podemos acceder y observamos que podemos hacer uso de `/bin/bash` con permisos `sudo` permitiendonos ser usuario `root`

![escalada de privilegios](./screenshots/13_root.png)