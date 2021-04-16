# 5.7.- Tareas del tema

!!! question "Creación de redes en docker"
    === "Enunciado"
        1. Crea una red de tipo *bridge* llamada `red1`, con  dirección de red `172.28.0.0`, máscara de red `255.255.0.0` y *gateway* `172.28.0.1`.
        2. Crea una red de tipo *bridge* llamada `red2` en la que el resto de parámetros sean proporcionados automáticamente por docker.
        3. Lista las redes disponibles para comprobar que se han creado correctamente.
        4. Muestra información detallada sobre la red `red1`, para ver su dirección de red, máscara y *gateway*.
        5. Muestra información detallada sobre la red `red2`, para ver su dirección de red, máscara y *gateway*.
    === "Solución"
        ```bash
        # Creación de la red red1
        > docker network create -d bridge --subnet 172.28.0.0/16 --gateway 172.28.0.1 red1
        # Creación de la red red2
        > docker network create -d bridge red2
        # Listar redes
        > docker network ls
        # Mostrar información detallada de la red red1
        > docker network inspect red1
        # Mostrar información detallada de la red red2
        > docker network inspect red2
        ```

!!! question "Conexión de redes a contenedores"
    === "Enunciado"
        1. Lanza un contenedor de la imagen `ubuntu:20.04` llamado `u1` que tenga como *hostname* `host1`, como ip `172.28.0.10` y que esté conectado a la red `red1`, creada en la tarea anterior.
        2. Entra en ese contenedor e instala la aplicación `ping` (`apt update && apt install inetutils-ping`).
        3. Muestra por pantalla la configuración de red del contendor `u1`.
        4. Lanza un contenedor de la imagen `ubuntu:20.04` llamado `u2` que tenga como *hostname* `host2` y que esté conectado a la red `red2`, creada en la tarea anterior, de manera que docker le proporcione la ip.
        5. Entra en ese contenedor e instala la aplicación `ping` (`apt update && apt install inetutils-ping`).
        6. Muestra por pantalla la configuración de red del contendor `u2`.
        7. Haz `ping` desde el contenedor `u2` al contenedor `u1`, tanto por ip como por *hostname*. ¿Es posible? ¿Por qué?.
        8. Conecta el contenedor `u1` a la red `red2`.
        9. Haz `ping` desde el contenedor `u2` al contenedor `u1`, tanto por ip como por *hostname. ¿Es posible? ¿Por qué?.
    === "Solución"
        ```bash
        # Lanzar el contenedor u1
        > docker run -it --name u1 --network red1 --hostname host1 --ip 192.28.0.10 ubuntu:20.04
        # Instalar ping en u1
        root@host1:/# apt update && apt install inetutils-ping && apt install net-tools
        # Mostrar configuración de red de u1
        root@host1:/# apt install net-tools
        root@host1:/# ifconfig
        root@host1:/# exit
        # Lanzar el contenedor u2
        > docker run -it --name u2 --network red2 --hostname host2 ubuntu:20.04
        # Instalar ping en u2
        root@host1:/# apt update && apt install inetutils-ping && apt install net-tools
        # Mostrar configuración de red de u2
        root@host1:/# apt install net-tools
        root@host1:/# ifconfig
        root@host1:/# exit
        # Hacer ping desde u2 hacia u1
        > docker exec -it u2 /bin/bash
        root@host2:/# ping host1
        # No es posible porque no están en la misma red
        root@host2:/# ping 192.28.0.10
        # No es posible porque no están en la misma red
        root@host2:/# exit
        # Conectar u1 a la red red2
        > docker network connect red2 u1
        # Hacer ping desde u2 hacia u1
        > docker exec -it u2 /bin/bash
        root@host2:/# ping host1
        root@host2:/# ping 192.28.0.10
        ```
