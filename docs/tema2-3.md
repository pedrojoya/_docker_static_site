# 2.3.- Asignando nombre a los contenedores

Hasta ahora cuando hemos puesto en ejecución los contenedores, la propia aplicación Docker ha sido la que se ha asignado un nombre por defecto al contenedor. Estos nombres constan de dos nombres aleatorios unidos por un guión bajo `_`, por ejemplo: `happy_golick`, `magical_mclean`, etc.

Evidentemente esto no es demasiado útil, ya que son nombres difíciles de recordar, que no tienen nada que ver con los contenedores que queremos lanzar e imposible de gestionar y memorizar cuando empezamos a tener muchos contenedores en nuestro sistema.

Por este motivo es conveniente que usemos siempre el flag `--name` cuando usamos la orden `docker run`. De esta manera, al usar nombres elegidos por nosotros mismos, nos serán más fáciles de recordar, ya que normalmente tendrán relación con la función que va a desempeñar dicho contenedor.

Pongamos varios ejemplos:

!!! success "Arranque de contenedores con nombre"
    ```bash
    # Se da el nombre servidorBD a un contenedor de la imagen mysql:8.0.22
    > docker run -d --name servidorBD -p 3306:3306 mysql:8.0.22
    # Se da el nombre servidorWeb a un contenedor de la imagen httpd:latest (Apache)
    > docker run -d --name servidorWeb -p 80:80 httpd
    ```
