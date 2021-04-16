# 2.8.- Obtener información detallada de un contenedor

Como hemos visto, con a orden `docker ps` podemos obtener información básica sobre los contenedores existentes. 

Si la información que hemos obtenido usando `docker ps` no nos es suficiente, deberemos usar la orden `docker inspect contenedor`, que nos va a dar una información detallada del contenedor indicado, ya sea por id o por nombre. Por ejemplo:

!!! success "Obtener información detallada sobre un contenedor"
    ```bash
    # Obtener información detallada sobre el contenedor llamado jenkins
    > docker inspect jenkins
    # Obtener información detallada sobre el contenedor con el id 5e5adf6815bc
    > docker inspect 5e5adf6815bc
    ```

Al ejecutar esta orden obtendremos en formato JSON información detallada sobre el contenedor, que incluye aspectos como: 

* el id del contenedor, 
* los puertos abiertos y sus redirecciones, 
* los *bind mounts* y volúmenes usados, el tamaño del contenedor, 
* la configuración de red del contenedor, 
* el *entry point*, que es lo que se ejecuta inicialmente en el contenedor al hacer `docker run`, 
* el valor de las variables de entorno.

Para formatear la salida podemos usar [Go Templates](https://golang.org/pkg/text/template/) y el flag `--format/-f`. Por ejemplo:

!!! success "Mostrar la ip del contenedor llamado jenkins"
    ```bash
    # Mostrar la ip del contenedor
    >  docker inspect --format 'La ip es {{.NetworkSettings.Networks.bridge.IPAddress}}' jenkins
    ```

que mostrará por consola lo siguiente:

!!! quote "Salida por consola"
    ```bash
    # Salida por consola
    La ip es 172.17.0.2
    ````

Otro ejemplo en el que se muestran las redirecciones de puertos del contenedor jenkins:

!!! success "Redirecciones de puertos del contenedor jenkins"
```bash
# Mostrar las redirecciones de puertos del contenedor
> docker inspect --format 'Las redirecciones de puertos son {{.NetworkSettings.Ports}}' jenkins
```

que produce la salida por consola:

!!! quote "Salida por consola"
    ```bash
    # Salida por consola
    Las redirecciones de puertos son map[50000/tcp:[{0.0.0.0 50000}] 8080/tcp:[{0.0.0.0 9090}]]
    ```

!!! warning "Aviso"
    Para poder definir bien el formato es necesario conocer en profundidad la estructura del objeto JSON retornado por `docker inspect`.
