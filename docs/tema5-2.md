# 5.2.- Obtener información sobre las redes

De igual manera que con las imágenes y los contenedores, podemos obtener información de las redes docker de maneras diferentes.

En primer lugar, podemos listar las redes docker disponbiles, mediante la orden `docker network ls`. Por ejemplo:

!!! success "Listar las redes docker disponibles"
    ```bash
    # Listar las redes docker disponibles
    > docker network ls
    ```

que por defecto produce la siguiente salida por consola:

!!! success "Salida por consola"
    ```bash
    NETWORK ID          NAME          DRIVER         SCOPE
    39d92743857a        bridge        bridge         local
    f11d627b86cf        host          host           local
    0796df5139aa        none          null           local
    ```

El listado, además del nombre de cada una de las redes creadas, recoge la siguiente información:

- El *network id*, que sirve para identificar una red y que se puede usar indistintamente con el nombre para cualquiera de las operaciones de gestión de redes (crear, borrar, obtener información, etc.)
- El *driver*, que, como ya dijimos en el apartado anterior, define el tipo de red que vamos a usar con los contenedores. Puede tomar los valores `bridge`, `host`, `macvlan`, `overlay` y `null`.
- El *scope*, que nos indica el ámbito de nuestras redes y que, en este caso, se trata de redes locales dentro de nuestra propia máquina.

Aunque, como veremos más adelante, podremos crear nuestras propias redes personalizadas, la instalación de Docker viene con tres redes ya creadas para nosotros:

* La red `bridge`, que usa un *driver* de tipo *bridge*, y es la red a la que se conectan por defecto los contenedores, si no especificamos una red distinta.
* La red `host`, que usa un *driver* de tipo *host*
* La red `none`, que no usa ningún *driver*

La red llamada `none`, no corresponde realmente a una red y no usa ningún *driver*, ya que su único cometido es aislar a los contenedores que la usan para que no se puedan conectar a ninguna red externa ni entre ellos, ni sean accesibles desde el exterior.

La orden `docker network ls` tiene algunas opciones interesantes, como `-q` (`--quiet`) para solo mostrar los id de las redes y `-f` (`--filter`) para filtrar la lista de manera que solo aparezcan los que cumplen una determinada condición. Veamos algunos ejemplos:

!!! success "Opciones avanzadas de docker network ls"
    ```bash
    # Mostrar solo el id de las redes
    > docker network ls -q 
    # Mostrar las redes con driver=bridge
    > docker network ls -f driver=bridge
    # Mostrar la red con nombre bridge formateada con Go Templates
    > docker network ls -f name=bridge --format 'La red por de defecto tiene el siguiente ID {{.ID}}'
    ```

Si queremos obtener información detallada sobre una red docker concreta, podemos usar la orden `docker network inspect`, al que le pasaremos el nombre o el id de una red, permitiéndonos formatear la salida usando [Go Templates](https://golang.org/pkg/text/template/), tal y como habíamos hecho en los temas anteriores cuando inspeccionábamos imágenes o contenedores. Veamos unos ejemplos:

!!! success "Opciones avanzadas de docker network ls"
    ```bash
    # Mostrar el tipo de driver de una red docker
    > docker network inspect mi_red --format 'El driver de {{.Name}} es {{.Driver}}'
    # Mostrar la dirección de red y la pasarela de una red docker
    > docker network inspect mi_red --format '{{.IPAM.Config}}'
    ```
