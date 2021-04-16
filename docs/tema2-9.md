# 2.9.- Mostrar logs de un contenedor

Las órdenes `docker ps` y `docker inspect` que hemos visto nos dan información relativa al contenedor, pero no nos dan información acerca de lo que está sucediendo en el contenedor. 

Normalmente para conocer los eventos que se están produciendo en una aplicación se usan lo que se conocen como *logs* o registros. 

Como vimos cuando hablamos de `docker run`, si lanzamos un contenedor de un servicio en segundo plano con `docker run -d` el contenedor comenzará a ejecutarse pero nosotros no veremos por pantalla los *logs* del servicio en cuestión.

Para estos casos, Docker nos proporciona la orden `docker logs contenedor`, que nos permite obtener los *logs* producidos en el indicador indicado, tanto si el contenedor está en ejecución como parado. Para indicar el contenedor podemos usar su id o su nombre.

Así en el siguiente ejemplo se mostrarán los *logs* de un contenedor llamado mariadb que está ejecutando el servicio de gestión de bases de datos MariaDB:

!!! success
    ```bash
    # Mostrar los logs del contenedor llamado mariadb
    > docker logs mariadb
    ```

Evidentemente, los *logs* varían mucho de un contenedor a otro, dependiendo del servicio del que se trate. No son iguales los *logs* de un contenedor de base de datos que los un contenedor correspondiente a un servidor web.

Como todas las órdenes, `docker logs` tiene más opciones, como por ejemplo:

!!! success "Opciones avanzadas de docker logs"
    ```bash
    # Opción -f o --follow . Sigue escuchando la salida que pueden dar los logs del contenedor
    > docker logs -f jenkins
    # Opción  --tail 5. Muestra las 5 últimas líneas de los logs del contenedor en cuestión
    > docker logs --tail 5 jenkins
    ```

La consulta de los *logs* es de especial utilidad cuando nuestros contenedores corresponden a servicios que están fallando.
