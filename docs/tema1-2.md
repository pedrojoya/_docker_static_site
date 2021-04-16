# 1.2.- Instalación de Docker

## Instalación de Docker en Windows

!!! info "Instalación de Docker en Windows"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/ozp84CCh0Uc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=ozp84CCh0Uc](https://www.youtube.com/watch?v=ozp84CCh0Uc)

## Instalación de Docker en Ubuntu

!!! info "Instalación de Docker en Ubuntu"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/PoRA7dAhhHA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=PoRA7dAhhHA](https://www.youtube.com/watch?v=PoRA7dAhhHA)

Tras realizar la instalación hay una serie de operaciones recomendadas. La primera de ella es ejecutar Docker como un usuario que no sea *root*, es decir, ejecutarlo sin `sudo`. Esto es fundamental, porque de lo contrario trabajar con Docker llega a ser un engorro.

!!! code "Ejecutar docker como usuario no root"
    ```bash
    # Crear el grupo docker si no se ha creado durante la instalación
    > sudo groupadd docker
    # Añadir nuestro usuario al grupo creado en el apartado anterior
    > sudo usermod -aG docker $user
    # Salir de sesión o reiniciar (en algunas máquinas virtuales).
    # Podemos también activar los cambios a los grupos
    > newgrp docker
    ```

Por otra parte, debemos habilitar o deshabilitar el servicio Docker al inicio, según nos interese. Por defecto el servicio se habilita al inicio y la sobrecarga sobre el sistema es mínima, así que recomendadmos dejarlo así si lo vamos a usar habitualmente.

!!! code "Habilitar o deshabilitar docker en el arranque"
    ```bash
    # Habilitar el servicio docker al iniciar el sistema (recomendado)
    > sudo systemctl enable docker
    # Deshabilitar el servicio docker.
    > sudo systemctl disable docker
    ```

## Instalación de Docker en Mac

Se recomienda seguir los pasos descritos en el siguiente enlace:

!!! info "Instalación de Docker en Mac"
    [https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)