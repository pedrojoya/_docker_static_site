# 6.5.- Fichero .dockerignore

Antes de que se ejecuten las órdenes `ADD` y `COPY` de los Dockerfile el proceso de construcción de docker build mira si en el contexto de la construcción existe un fichero `.dockerignore`. 

El funcionamiento de este tipo de ficheros es análogo al funcionamiento de los ficheros `.gitignore`, que excluyen una serie de ficheros del control de versiones, pero aplicado al caso de la construcción de imágenes, de manera que dichos archivos serán excluidos de la copia de arhivos a la imagen.

Los archivos que se indiquen en el fichero `.dockerignore` no pasarán a la imagen en el proceso de construcción de la misma. 

Veamos un ejemplo de fichero `.dockerignore` genérico:

!!! success "Fichero .dockerignore genérico"
    ```bash
    # La carpeta app tiene el contenido de un repositorio
    # Se excluye la carpeta .git
    app/.git
    # Se excluye el fichero .gitignore
    app/.gitignore
    # Se excluyen todos los archivos dentro de la carpeta log pero dejo la carpeta
    app/log/*
    # Se excluyen todos los archivos dentro de la carpeta tmp pero dejo la carpeta.
    app/tmp/*
    # Se excluye el archivo README.md
    app/README.md
    ```

Y ahora un ejemplo concreto para una aplicación NodeJS:


!!! success "Fichero .dockerignore para proyecto NodeJS"
    ```bash
    # La carpeta nodeapp tiene el contenido de un repositorio
    # Se excluye la carpeta .git
    nodeapp/.git
    # Se excluye el fichero .gitignore
    nodeapp/.gitignore
    # Se excluye la carpeta node_modules. Eso me obliga a hacer npm install al arrancar el contenedor
    nodeapp/node_modules
    # Se excluye el fichero creado por el editor de código
    .vscode
    ```

Para más información sobre los comodines y reglas que puedo tener en un fichero `.dockerignore` visiten siguiente enlace: [https://docs.docker.com/engine/reference/builder/#dockerignore-file](https://docs.docker.com/engine/reference/builder/#dockerignore-file)