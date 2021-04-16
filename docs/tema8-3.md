# 8.3.- Seguridad al arrancar los contenedores

Si queremos aumentar la seguridad al desplegar los contenedores podemos actuar en dos sentidos.

El primero de ellos es limitar los recursos que puede usar el contenedor, lo que será de especial utilidad para evitar ataques de DoS (*Denial Of Service*, negación de servicio). 

Para ello tenemos disponibles una serie de *flags* en la orden `docker run`, entre los que destacamos:

* `--memory` o `-m`, que establece el límite de memoria que puede llegar a usar un contenedor.
* `--memory-reservation`, que es similar al anterior pero es un límite blando. Si se sobrepasa docker intentará reducir la memoria consumida por el contenedor.
* `--cpus`, que limita el número de cpus del sistema que un contenedor puede utilizar.

Veamos un ejemplo:

!!! success "Establecer límite de memoria y de número de cpus al arrancar un contenedor"
    ```bash
    # Arrancar un contenedor con un límite de 4GB y 4 CPUs
    > docker runt -it -m 4Gb --cpus=4 httpd .....
    ```

Otra opción se seguridad es **deshabilitar determinadas capacidades del contenedor** que vamos a arrancar. Para ello usaremos el *flag* `--cap-drop`.

A continuación indicamos algunas de las capacidades más importantes que podemos habilitar o deshabilitar:

* `audit_write`: Escribe mensajes en los log de auditoría del Kernel.
* `chown`: Para permitir cambios en el UID y GUID de los ficheros.
* `net_bind_service`: Permite asociar un socket a un puerto que puede ser accedido desde Internet.
* `net_admin`: Configuración de interfaces, masquerading, enrutamiento, iptables etc...
* `setuid`: Manipulación del UID de los procesos etc...
* `setguid`:  Manipulación del GUID de los procesos etc..
* `fowner`: Para manipulación de todo tipo de permisos y ACLs en ficheros.

Veamos un ejemplo:

!!! success "Deshabilitar la escritura de mensajes en el log de auditoría del Kernel"
    ```bash
    # Deshabilitar la escritura de mensajes en el log de auditoría del Kernel
    > docker runt -it --cap-drop=audit_write  ubuntu:20.04
    ```

Este *flag* hace justo lo contrario al *flag* `--cap-add`, que ya estudiamos cuando estudiamos cómo habilitar *iptables* usando la capacidad `NET_ADMIN`.

Habilitar o deshabilitar capacidades requiere de un estudio minucioso para cada contenedor. Como norma general, debemos limitar al máximo las capacidades cuando llevemos esos contenedores a producción.

Para consultar una lista completa de las capacidades de los sistemas Linux visite el siguiente enlace : [https://man7.org/linux/man-pages/man7/capabilities.7.html](https://man7.org/linux/man-pages/man7/capabilities.7.html)
