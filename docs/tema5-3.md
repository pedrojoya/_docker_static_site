# 5.3.- Gestión de redes docker

En un apartado anterior comentamos que en docker tenemos creadas de antemano tres redes, entre la que destacábamos la red `brige` porque es la red a la que se conectan por defecto todos los contenedores a no ser que indiquemos lo contrario y porque normalmente nos ofrece la funcionalidad habitual que vamos a necesitar en nuestros contenedores.

Aunque la red `bridge` es cómoda de usar, en vez de usar dicha red común, es preferible crear para cada aplicación su propia red personalizada de tipo *bridge*, a través de la que se comuniquen los distintos contenedores que conformen la aplicación.

Las redes con *driver* `bridge` definidas por el usuario tienen una serie de ventajas frente a la red `bridge` usada por defecto:

- Proporcionan resolución DNS entre los contenedores, mientras que la red *bridge* predefinida no la proporiciona, a no ser que usemos opciones consideradas *deprectated*.
- Permiten la conexión en caliente de contenedores a la red, es decir mientras están en ejecución, algo que no es posible en la red *bridge* por defecto, que precisa que paremos el contenedor previamente.
- Permiten gestionar de manera más segura el aislamiento de los contenedores en comparación con la red *bridge* por defecto, en la que convivirán servicios que no tienen nada que ver entre sí.
- Proporcionan un mayor control sobre la configuración, en comparación con la red *bridge* por defecto, en la que todos los contenedores comparten todos la misma configuración de red (MTU, reglas ip tables, etc.).
- Evitan los conflictos que pueden producirse en la red *bridge* por defecto por el hecho de que todos los contenedores compartan ciertas variables de entorno.

## Creación de redes 

Para crear una nueva redes docker usaremos la orden `docker network create nombreRed`. Por ejemplo:

!!! success "Creación de una nueva red denominada red1 con las opciones por defecto"
    ```bash
    # Crea la red red1 con las opciones por defecto
    > docker network create red1 
    ```

Existen distintas opciones que podemos indicar para la red, como por ejemplo:

* `-d` o `--driver`, para indicar el *driver* que debe usar la red. El valor por defecto es `bridge`.
* `--scope`, para indicar el ámbito, que por defecto es `local`.
* `--subnet`, para indicar la IP de la subred y la máscara de red. Si no lo indicamos, docker la eligirá por nosotros.
* `--gateway`, para indicar el *gateway*. Si no lo indicamos, docker lo eligirá por nosotros.

Veamos un ejemplo más específico:

!!! success "Crea la red red2 indicando driver bridge, dirección de la subred, máscara de red y gateway"
    ```bash
    # Crea la red red2 indicando driver bridge, dirección y máscara de red y gateway
    > docker network create -d bridge --subnet 172.24.0.0./16 --gateway 172.24.0.1 red2
    ```

Cada red docker creada establece un puente de red específico para cada red, que podemos consultar usando con la orden `ifconfig / ip a`.

Para obtener una descripción más detallada de lo todas las opciones disponibles para esta orden debemos visitar la [referencia completa de redes en docker ](https://docs.docker.com/network).

## Eliminación de redes

Para eliminar una red docker concreta usaremos la orden `docker network rm`, que recibe el nombre o el id de una red. Por ejemplo:

!!! success "Eliminación de una red"
    ```bash
    # Eliminar la red red1 
    > docker network rm red1 
    ```

Es importante resaltar que **no es posible eliminar una red que tenga contenedores asociados**. Antes tendremos que eliminar los contenedores o desconectarlos de la red.

Si queremos eliminar de una vez todas las redes que no tengan asociado ningún contenedor, podemos usar la orden `docker network prune`. Por ejemplo:

!!! success "Eliminar todas la redes que no tengan contenedores asociados"
    ```bash
    # Eliminar todas la redes que no tengan contenedores asociados
    > docker network prune
    ```

También podemos indicar con la opción `-f` o `--force` que no se nos solicite confirmación en la eliminación, y con la opción `--filter` indicar la condición que deben cumplir las redes para que sean eliminadas. Por ejemplo:

!!! success "Eliminación de una red"
    ```bash
    # Eliminar todas las redes que no tengan contenedores asociados sin preguntar confirmación
    > docker netowkr prune -f
    # Eliminar todas las redes que no tengan contenedores asociados y que fueron creadas hace más de 1 hora
    > docker network prune --filter until=60m
    ```
