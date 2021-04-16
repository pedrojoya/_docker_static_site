# 4.6.- Depurando aplicaciones con bind mounts

En los apartados anteriores hemos visto como montar en un contenedor el directorio con el código en desarrollo, para que lo hemos utilizado *bind mounts*.

Sin embargo, para poder afrontar con garantías el proceso de desarrollo vamos a necesitar en algún momento depurar nuestro código, para lo que va a ser necesario que conectemos nuestro editor de código o IDE con el contenedor y que instalemos las utilidades necesarias.

Dicho proceso dependerá evidentemente del IDE, del lenguaje de programación y del depurador que vayamos a usar, pero vamos a ver un par de ejemplos ilustrativos.

En el siguiente vídeo se muestra cómo depurar desde Visual Studio Code una aplicación web PHP que se despliega en un contenedor con PHP Apache:

!!! info "Depuración PHP en contenedor con Visual Studio Code"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/OBvVSUYDq5s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=OBvVSUYDq5s](https://www.youtube.com/watch?v=OBvVSUYDq5s)

Hemos usado los siguientes ficheros de configuración. El primero el fichero [launch.json](https://gist.github.com/pekechis/8f80336fa0f357df54acb3b680327fa2) para Visual Studio Code.

```json
{
    "version": "0.2.0",
    "configurations": [
        
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",                      
            "port": 9003,
            "pathMappings": {
                "/var/www/html": "/path/to/php/folder"
            }
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 9003
        }
    ]
}
```

Y el fichero de configuración [xdebug.ini](https://gist.github.com/pekechis/48e1d37125f6082e5c4cf004a8bec7e3) para el contenedor.

```ini
zend_extension=xdebug
xdebug.mode = debug
xdebug.start_with_request = yes
xdebug.log = /tmp/xdebug.log
xdebug.client_host = 172.17.0.1 
xdebug.client_port = 9003
```

Por último veamos como depurar una aplicación python django también en Visual Studio Code:

!!! info "Depuración python django en contenedor con Visual Studio Code"
    <iframe width="100%" style="aspect-ratio: 16 / 9;" src="https://www.youtube.com/embed/LJ1_hxw1c38" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    
    Enlace: [https://www.youtube.com/watch?v=LJ1_hxw1c38](https://www.youtube.com/watch?v=LJ1_hxw1c38)

Para otros editores de código, IDEs y lenguajes de programación el proceso es similar, la clave está en configurar una depuración remota, ya que nuestro código va a estar situado en un contenedor.
