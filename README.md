# Manual Para aprender Docker

## Primeros pasos

- Para poder ver el estado de docker en nuestra maquina:
sudo systemctl status docker
o tambien:
```docker
sudo service docker status
```

- Para poder iniciar docker si es que esta de forma manual:
```docker
sudo systemctl start docker
```

o tambien:
```docker
sudo service start docker
```

- En caso de reiniciarlo o que algo este mal:
```docker
sudo systemctl restart docker
```

o tambien:
```docker
sudo service docker restart
```

- Para alias:
```docker
alias docker="sudo docker"
```

- Para ver los contenedores que estan corriendo:
```docker
docker ps
```

NOTA: En caso de un permiso denegado y un mensaje como este:


Entonces vemos el [video](https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket) para poder resolver este problema.

O podemos seguir estos pasos:
- Vamos a la terminal y escribimos: 
```bash
sudo usermod -aG docker ${USER}
```

- Luego escribimos:
```bash
su - ${USER}
```

Nos dara un espacio para escribir la contraseña del usuario.
- Por ultimo revisamos si ya nos deja docker:
```docker
docker ps
```
Y listo
**HAPPY HACKING!!!**

## Comandos más utilizados
- Para listar los contenedores que estan corriendo al instante: 
```docker
docker ps
```

- Para listar todos los contenedores que estan o corrieron alguna vez: 
```docker
docker ps -a
```

- Para ver toda la descripcion de contenedor especifico:
```docker
docker inspect <CONTAINER-ID>
```

- Para ver descripcion de contenedor especifico por medio de alias: 
```docker
docker inspect <NAMES>
```

Ejemplo:

<pre>docker inspect hello-world</pre>

- Para crear un contenedor con un nombre (alias): 
```docker
docker run --name test hello-world
```

- Para renombrar el un contenedor:
```docker
docker rename <old-name> <new-name>
```

Ejemplo: 
<pre>docker rename test daniel</pre>

- Para eliminar contenedores por ID o NAME:
```docker
docker rm <ID o NAME>
```

- Para eliminar todos los contenedores que estan inactivos: 
```docker
docker container prune
```

## Modo interactivo

Verificamos que este corriendo Docker:
```docker
sudo service docker status
```

En caso de que no, lo iniciamos:
```docker
sudo service docker start
```

Corremor un contenedor de ubuntu dentro de Docker:
```docker
docker run ubuntu
```

Si colocamos:
```docker
docker -it ubuntu
```
| Abreviacion  | Significado |
|  :---:  |  :----:  |
| -i  |  Es interactivo  |
| -t  | Es para abrir una terminal |


Podremos acceder a un modo interactivo, estaremos dentro de ubuntu dentro de una maquina virtual, para verificar eso
colocamos:
```bash
ls
```
y nos daremos cuenta que son las carpetas de ubuntu
Para ver que enserio estamos dentro de ese contenedor de Ubuntu en caso no creas puedes ver la version de Ubuntu que se instalo en el contenedor de Docker:
```bash
cat /etc/lsb-release
```

Ese comando nos dara una descripcion de que estamos trabajando con el contenedor de Docker y la informacion de Ubuntu.

Para ver que ese contenedor esta corriendo en tiempo real, podemos abrir otra terminal y colocar lo que haciamos antes:
```bash
docker ps
```

Entonces tendremos la informacion de que el contenedor de Ubuntu esta corriendo desde un cierto tiempo.

## Limitar memoria en Docker
Para poder limitar la memoria de Docker lo podemos hacer desde su creacion. Cuando corremos el comando **"docker run"** usando _flags_ de: **Runtime constraints on resource**
y se veria algo asi:

```docker
docker run --memory="100m" --cpus=0.5 <image>
```

Podemos ver mas de esto en la [documentacion oficial](https://docs.docker.com/engine/reference/run/#runtime-constraints-on-resources):

## Ciclos de vida de un contenedor

Un contenedor cuando lo ejecutamos la mayoria de veces solo se ejecuta y con la misma se cierra
entonces para poder evitar eso podemos hacer que el contenedor de Docker lo deje corriendo aun asi
si no estamos dentro de el, vamos a realizar el ejemplo con un contenedor de Ubuntu:

```docker
docker run --name <alias o nombre del contenedor> -d ubuntu <comando para correr>
```
- Ejemplo:

    <pre>docker run --name miContenedor -d ubuntu tail -f /dev/null</pre>

Podemos ejecutar:

```docker
docker ps
```

Y veremos que ahora el contenedor en la columna **STATUS** dice **UP**, eso quiere decir que esta corriendo nuestro contenedor
sin estar dentro de el, **pero que pasa si quiero trabajar dentro del contenedor?**
Podemos hacer uso del siguiente comando para entrar:

```docker
docker exec -it <ID, nombre o alias del contenedor> <comando a ejecutar dentro del contenendor>
```

- Ejemplo:

    <pre>docker exec -it miContenedor bash</pre>

Y con eso estaremos de nuevo dentro de una terminal dentro de nuestro contenedor de Ubuntu de Docker.
Y ahora **¿Como hacemos para que ese contenedor se cierre, que deje de ejecutarse?**, lo podemos hacer de varias maneras
dependiendo de el sistema operativo que estemos utilizando, pero primero debemos saber el ID del proceso que tiene
nuestro contenedor, entonces usamos el siguiente comando:

```docker
docker inspect --format '{{.State.Pid}}' <nombre o alias del contenedor>
```
- Ejemplo:
    <pre>docker inspect --format '{{.State.Pid}}' miContenedor</pre>

Ahora si podremos detener el contenedor:

- Si estas en linux puedes usar alguno de estos:
(si no tienes permisos de administrador)
```bash
sudo kill <ID> 
```

(en caso no te deje el comando anterior)

```bash
sudo kill -9  <ID> 
```

(si eres administrador)

```bash
kill <ID> 
```

```bash
docker kill <ID>
```

```bash
sudo kill -9 "$(sudo docker inspect --format '{{.State.Pid}}' alwaysup)"
```

- Si estas en Windows:

```bash
docker stop <nombre o alias del contenedor>
```

-Si estas en MacOS:

```bash
docker kill <ID, nombre o alias del contenedor>
```

#### ¿Que significa eso de -f /dev/null?
/dev/null es conocido como un **"agujero negro"** es decir que ese archivo no tiene nada y no habra problemas
para ejecutar algo.
No solo existe ese, hay varios:

- /dev/full
- /dev/zero
- /dev/random

## Exponiendo contenedores

- Comandos para exponer contenedores:

- Para correr un nginx
```bash
docker run -d --name proxy nginx
```

- Para apagar el contenedor
```bash
docker stop proxy
```

- Para borrar el contenedor
```bash
docker rm proxy (borro el contenedor)
```

- Para pararlo o borrarlo
```bash
docker rm -f <contenedor>
```

Para correr el contenedor y exponerlos en un puerto del contenedor y un puerto de mi máquina
```bash
docker run -d --name proxy -p 8080:80 nginx
```
**NOTA:** el comando anterior donde dice **-p 8080:80** quiere decir:

    > Docker quiero que me ejecute el servidor en el puerto 8080 **de mi máquina** y **dentro del contenedor ejecutalo en el puerto 80**.

Para comprobar que funciono, verifico en el navegador de mi computadora, en la barra de direcciones:
```bash
localhost:8080
```

Para ver los logs
```bash
docker logs proxy (veo los logs)
```

Hacer un follow del log
```bash
docker logs -f proxy
```
**NOTA:** el __flag__ **-f** del comando anterior quiere decir **follow**

Ver y seguir las 10 últimas entradas del log
```bash
docker logs --tail 10 -f proxy
```

Para agregar un poco a la exposición de puertos, también lo podemos hacer de manera aleatoria utilizando **-P**
Podemos exponer un contenedor por un puerto aleatorio que este libre en nuestro Docker Server:
- Para exponer un contenedor por un puerto random:
```bash
docker run -dP nginx
```

- Para ver los puertos expuestos del contenedor:
```bash
docker port proxy
```

## Volumenes

Los volúmenes son el mecanismo preferido para conservar los datos generados y utilizados por los contenedores de Docker. Si bien los montajes de enlace dependen de la estructura del directorio y el sistema operativo de la máquina host, Docker administra completamente los volúmenes. Los volúmenes tienen varias ventajas sobre los montajes vinculantes:

- Ventajas

    - Mayor seguridad al compartir archivos entre contenedores, los archivos los maneja directamente docker esto implica que, cualquier persona no puedes acceder a estos archivos.    
    - Los volúmenes son más fáciles de respaldar o migrar que enlazar montajes.
    - Puede administrar volúmenes mediante los comandos de la CLI de Docker o la API de Docker.
    - Los volúmenes funcionan tanto en contenedores de Linux como de Windows.
    - Los volúmenes se pueden compartir de forma más segura entre varios contenedores.
    - Los controladores de volumen le permiten almacenar volúmenes en hosts remotos o proveedores en la nube, para cifrar el contenido de los volúmenes o para agregar otras funciones.
    - Los nuevos volúmenes pueden tener su contenido pre cargado por un contenedor.
    - Los volúmenes en Docker Desktop tienen un rendimiento mucho mayor que los montajes de enlace de hosts de Mac y Windows.
Además, los volúmenes suelen ser una mejor opción que los datos persistentes en la capa de escritura de un contenedor, porque un volumen no aumenta el tamaño de los contenedores que lo usan y el contenido del volumen existe fuera del ciclo de vida de un contenedor determinado.

- Desventajas
    - Una de las desventajas es que si se desea visualizar los archivos desde la máquina hosts es mucho más complicado.

## Comandos

Para listar volumnes
```bash
docker volume ls
```

Crear un volumen
```bash
docker volume create <volume_name>
```

Montar volumen en un contenedor
```bash
docker run -d --name db --mount src=<volume_name>,dst=/data/db mongo
```

| Abreviacion  | Significado |
|  :---:  |  :----:  |
| src | Indica la direccion en donde esta el volumen  |
| dst | Indica el destino de donde se guardara el volumen |

Para inspeccionar el contenedor
```bash
docker inspect <name_container>
```

- Ejemplo
    <pre>docker inspect db</pre

Insertamos datos
```bash
docker exec -it db bash
```

```bash
mongosh
```

```bash
use pruebas
```

```bash
db.users.insert({"nombre": "Melany", "apellido": "Salazar", "edad": 20})
```

```bash
db.users.find()
```

```bash
exit
```

Borramos el contenedor ya que tenemos el volumen donde se guardan los datos
```bash
docker rm -f db
```

Verificamos si funciono
```bash
docker run -d --name db --mount src=dbdata,dst=/data/db mongo
```

```bash
docker exec -it db bash
```

```bash
mongosh
```

```bash
user pruebas
```

```bash
db.users.find()
```

Y podremos ver en la terminal que la informacion se guardo dentro del volumen

### Pasos para crear volumenes


[Aprende más sobre los volumnes en Docker](https://docs.docker.com/storage/volumes/)

## Bind mounts con MongoDB

Corremos y descargamos el contenedor de Mongo
```bash
docker run -d --name db mongo
```

Verificamos que el contenedor de mongo este corriendo
```bash
docker ps -a
```

Ejecutamos mongo
```bash
docker exec -it db bash
```
**NOTA:** a partir de la version 6.0 para ejecutar el bin en mongo
se se utiliza la palabra: "mongosh"

Para iniciar en mongo usamos el comando anterior:
```bash
mongosh
```

### Comandos para mongoDB

Ver las basesde datos creadas
```bash
show dbs
```

Crear una nueva base de datos
```bash
use <name>
```

Para insertar un registro
```bash
db.user.insert({"campo":"nombre"})
```

**NOTA:** puede que lance un mensaje de advertencia de utilizar
alguno de estos comandos: __"insertOne"__, __"insertMany"__, __"bulkWrite"__

Para ver los registros guardados
```bash
db.user.find() (para ver los registros guardados)
```

## Borrar un contenedor de manera forzada

Si queremos eliminar un contenedor pero esta activo lo podemos hacer de la siguiente manera:
```bash
docker rm -f <nombreContenedor>
```
- Ejemplo

<pre>docker rm -f db</pre>

Lo malo de borrar el contenedor de mongo es que los datos que
guardamos en la base de datos ya no estaran, podemos repetir los
pasos par instalar de nuevo el contenedor de mongo:

```bash
docker run -d --name db mongo
```

Luego los mismos pasos para ingresar a mongo:

```bash
docker exec -it db bash
```

[Aprende más sobre como borrar volumnes, contenedores e images en Docker](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes-es)

## ¿Como hacemos para que los datos persistan en el contenedor de mongoDB?

Hay una manera para hacer que los datos persistan:

1. Creamos una carpeta nueva:
```bash
mkdir mongodata
```

2. Obtenemos la ruta absoluta de la carpeta en donde estamos:
```bash
pwd
```

3. La copiamos y hacemos lo siguiente:
```bash
docker run -d --name db -v <ruta del pwd>/mongodata:/data/db mongo
```

## Copia de archivos y carpetas a contenedores

Para este ejemplo utilizaremos un contenedor de ubuntu.
Para poder copiar un archivo de mi maquina virtual al contenedor de docker lo que se hace es lo siguiente:

1. Vamos a correr el comando
```bash
docker run -d --name copytest ubuntu -f /dev/null
```

2. Vamos a verificar que el contenedor este corriendo
```bash
docker ps
```

3. Ingresamos al contenedor de Ubuntu
```bash
docker exec -it copytest bash
```

4. Una vex dentro creamos una carpeta en donde vamos a guardar nuestro archivo
```bash
mkdir pruebas
```

5. Salimos del contenedor de Ubuntu
```bash
exit
```

6. Nos dirigimos a la carpeta donde esta el archivo que queremos copiar, en mi caso es asi
```bash
cd Documents/practicasDocker
```

7. Corremos el siguiente comando para indicar que queremos copiar el archivo al contendor de docker:
```bash
docker cp <archivoParaCopiar> <nombreContenedor>:/<rutaDestinoCopia>
```

- Ejemplo

    <pre>docker cp saludar.txt copytest:/pruebas/copia.txt</pre>

8. Para verificar que el archivo de copia.txt fue agregado exitosamente al contenedor podemos hacerlo de la siguiente manera
```bash
docker exec -it copytest bash
```
```bash
cd pruebas
```
```bash
ls
```

De esta manera veremos que si se ha copiado el archivo 

## Copiar directorios

Se realiza de la misma forma en que se copia un solo archivo

**NOTA:** para poder copiar una carpeta nos ubicamos un nivel inferior de ella, por ejemplo

- Tengo una carpeta de "Mis Documentos" llamada examenes, entonce para poder copiarla solo tengo que ubicarme de esta manera
```bash
cd Documents
```

- Luego de eso procedo a copiar la carpeta
```bash
docker cp examenes copytest:/
```

Y listo, pero en caso querramos copiar esa carpeta en otra carpeta dentro del contenedor se puede hacer de esta manera
```bash
docker cp examenes copytest:/pruebas/examenesCopias
```

**NOTA:** Se le cambio de nombre a la carpeta a la hora de copiarlo dentro del contenedor.

## Imagenes

Las imagenes son como Docker intenta arreglar los 2 principales problemas, que son los de **distribución y construcción** de software.
### ¿Qué es una imagen en Docker?
- Son como plantillas o moldes a partir de las cuales docker crea contenedores.
- Es una pieza de software empaquetada de forma liviana.
- Contiene todo lo necesario para que un contenedor pueda ejecutarse exitosamente.
- Las imagenes son las plantillas a partir de las cuales se crean nuevos contenedores.

### Comandos
Para ver las imagenes que tenemos en el computador
```bash
docker image ls
```

Para bajar una imagen de una versión específica
```bash
docker pull <contenedor>:<version>
```

- Ejemplos

    <pre>docker pull ubutun:20.04</pre>
    <pre>docker pull node:18.14.0</pre>
    <pre>docker pull postgres:13.10</pre>

## Crear mi propia imagen

Ese proceso siempre estara basado en **docker-file** y el comando que se encarga de crearla es **build** pero ¿Por qué creamos la imagen? se hace para correr los contenedores

1. Creamos una carpeta para nuestra imagen
```bash
mkdir images && cd images
```
2. Creamos un nuevo docker-file
```bash
touch Dockerfile
```
3. Abrimos el archivo en VS Code o cualquier editor de codigo

Abrir VS Code
```bash
code .
```

Abrir nano
```bash
nano Dockerfile
```

Abrir con VIM
```bash
vim Dockerfile
```

4. Siempre que creamos un contenedor debemos indicar de que sera la imagen, si se require descargar cierto software antes, en este caso se hara una imagen nueva basada en ubutu
```bash
FROM ubuntu:latest 

RUN touch /usr/src/saludar.txt
```

**NOTA:** _RUN touch /usr/src/saludar.txt_ se ejecuta en tiempo de build

5. Hacemos el build y aplicamos una tag
```bash
docker build -t ubuntu:test .
```

### ¿Qué significa el ubutu:platzi? 
Es una nomenclatura que se utiliza en docker para dar un tag a la imagen que se va a crear en este caso estamos diciendo que vamos a crear una imagen de ubuntu con el tag test

|Abreviación| Significado| 
| :--: | :--: |  
| -t | Es una etiqueta que tendra la imagen (tag) |

6. Corremos el contenedor de la imagen que creamos
```bash
docker run -it ubuntu:test
```

7. Verificamos que el archivo _saludar.txt_ este dentro de la imagen
```bash
ls /usr/src/
```

8. Para subir la imagen debemos cambiar el nombre de la imagen ya que si lo dejamos como _ubuntu:test_ esto lo entiende Docker como que estaremos subiendo esta imagen al repo oficial de ubuntu, pero no queremos eso, queremos que sea en nuestro Docker Hub

9. Cambiamos el nombre de la imagen
```bash
docker tag ubuntu:test dnoriega20/ubuntu:test
```

**NOTA:** _dnoriega20/ubuntu:test_ es una convención, una nomenclatura que tiene para saber a que repositorio de Docker quiero pushear mi imagen _dnoriega20_ es mi usuario en Docker, pero debes colocar el tuyo.

10. Vemos el nuevo nombre de nuestra tag
```bash
docker image ls
```

11. Subimos nuestra imagen en Docker Hub
```bash
sudo docker login -u <nombreUsuario> -p <password>
```

**NOTA:** el nombre de usuario y password deben de ser los de tu cuenta de Docker

12. Agregamos nuestra imagen a Docker Hub
```bash
docker push <nombreUsuario>/ubunt:test
```

Y listo tendremos nuestra primera imagen en Docker Hub
