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

## El sistema de capas

Las imágenes son un conjunto de capas, y a partir del Dockerfile se puede saber como está construido una imagen. Existen distintas maneras de ver las capas que conforman una imagen:

1. Si la imagen es pública se puede visitar Dockerhub y buscar una imagen para ver su Dockerfile. Una vez encontrar la imagen buscamos en los tag y haciendo clic en los tag se mostrará el docker file para construir dicha imagen.

2. Se puede hacer a través de la línea, esta opción no es muy cómoda. Es importante destaca que los cambios se presenta por filas y las filas más inferiores son los cambios más viejos y las que están más arriba son los cambios más recientes. el comando es
```bash
docker history <nombreImagen:tag>
```
- Ejemplo 
    <pre>docker history dnoriega20/ubuntu:test></pre>

Otra forma de poder ver ese historial es con una herramienta externa llamada **dive** te dejo la [documentación](https://github.com/wagoodman/dive) para poder instalarlo y varios atajos de teclado en esta herramienta.

### Usando dive

Ver el historial de una imagen
```bash
dive ubuntu:test
```
 Para navegar dentro de esta herramienta es por medio de las flechas y la tecla Tab.

 ## Utilizando la cache en Docker
 Para aprvechar al 100% Docker podemos hacer uso del cache que se ejecuta siempre al realizar cambios dentro del codigo, por ejemplo para evitar estar haciendo el _docker build -t miapp_ solo por cambiar una linea de codigo y esperar a que se empaquete todo en el contenedor de nuevo podemos hacer el alchivo _Dockerfile_ de la siguiente manera:
 ```bash
FROM node:14

COPY ["package.json", "package-lock.json", "/usr/src/"]

WORKDIR /usr/src

RUN npm install

COPY [".", "/usr/src"]

EXPOSE 8080

CMD ["npx", "nodemon", "index.js"]

 ```

 __NOTA:__ Una mejor explicacion es que al hacer la copia solo de los _package_ es para evitar que al realizar el build con docker tengamos que estar en una espera mayor ya que al hacer esto estamos diciendole que copie las dependencias primero, para despues hacer la instalacion de las mismas con Node, luego de eso, podemos copiar todos los archivos que se modificaron, mientras que le damos la orden de que se ejecute mediante nodemon para que se actualice de manera automatica sin necesidad de estar deteniendo el contenedor y volver a iniciarlo desde 0.

 ## Docker Networking

Bueno, un resumen de que el el network en Docker, practicamente es crear un localhodt vitrual y conectar ahi todas las apps o máquinas que quieras, es como si la red fuese un router virtual y cada app puede acceder puede acceder a ese router (red).

Tambien conocidas como redes virtuales (redes de docker)
```bash
docker network
```

Para listar las redes que tenemos en Docker usamos el siguiente comando
```bash
docker network ls
```

__NOTA:__ habran algunas redes ya que son por defecto

Para crear nuestra propia red
```bash
docker network create --attachable mired
```

__NOTA:__ _--attachable_ permite que otros contenedores se puedan conectar a la red nueva que estoy creando

Para inspeccionar las redes
```bash
docker network inspect <nombreDeRed>
```

Para este ejemplo utilizaremos una basede datos de MongoDB, entonces creamos el contenedor para esa base de datos.
```bash
docker run -d --name monguito mongo
```

Conectar un contenedor a una red
```bash
docker network connect <nombreDeRed> <nombreContenedorParaConectar>
```

__NOTA:__ el codigo anterior quiere decir: quiero que en esta red, conectes este contenedor.

Ahora lo unico que hace falta hacer es correr la aplicacion conectada a esa red

```bash
docker run -d --name app -p 8080:8080 --env MONGO_URL=mongodb://monguito:27017/test miapp
```

__NOTA:__ --env es para agregar una variable de entorno porque el archivo _index.js_ contiene esa variable de entorno _MONGO_URL=mongodb://_ espara especificar la ruta del contenedor, en este caso el nombre del contenedor de la base de datos es _monguito_ con el puerto _27017_ y _miapp_ es para decirle a que imagen quiero que se conecte

Por ultimo para ver si el contenedor esta corriendo nos dirigimos al navegador de tu preferencia, en mi caso sera Chrome y en la barra de direcciones colocamos 
```bash
localhost:8080
```

¿Te funciono? Por supuesto que no!!!
Aun nos falto un paso muy importante y es agregar ese contenedor que agregamos en el codigo anterior, entonces basta con hacer lo que ya aprendimos para agregar contenedores a una red, en este caso lo agregaremos a la red llamada _mired_:

```bash
docker network connect mired app
```

Inspeccionamos la red
```bash
docker network inspect
```

Y podremos ver que ya esta agregado, volvemos al navegador y colocamos lo de siempre:
```bash
localhost:8080
```

Listo, esta vez si funciona!

## Docker Compose: la herramienta todo en uno

Es una herramienta que permite simplificar el uso de Docker. A partir de archivos YAML es mas sencillo crear contendores, conectarlos, habilitar puertos, volumenes, etc.

Con Compose puedes crear diferentes contenedores y al mismo tiempo, en cada contenedor, diferentes servicios, unirlos a un volúmen común, iniciarlos y apagarlos, etc. Es un componente fundamental para poder construir aplicaciones y microservicios.

Docker Compose te permite mediante archivos YAML, poder instruir al Docker Engine a realizar tareas, programáticamente. Y esta es la clave, la facilidad para dar una serie de instrucciones, y luego repetirlas en diferentes ambientes.

__NOTA:__ Si estas utilizando Docker en Windows o Mac, no tendras problemas al utilizar Docker Compose, pero si estas utilizando en alguna distribucion de Linux, es probable que tenga que instalarlo de forma manual, pero para verificar que tengas Docker Compose utilizaremos el siguinte comando
```bash
docker compose version
```

Te tendria que aparecer una version, en caso de no ser asi, te dejo la [documetaciòn](https://docs.docker.com/compose/install/) oficial de Docker para poder instalarlo en caso tengas problemas con ello.

Ahora nos dirigimos al proyecto que tenemos con Docker y en el archivo de _index.js_ recordemos que tenemos la conexion al puerto 8080, entonces tendremos que modificar algunas cosas en el proyecto, en este caso vamos a editar el archivo _docker-compose.yml_ de la siguiente manera
```bash
vim docker-compose.yml
```

Luego de esto nos el archivo esta compuesto de algo como esto:
```bash
version: "3.8"

services:
  app:
    image: platziapp
    environment:
      MONGO_URL: "mongodb://db:27017/test"
    depends_on:
      - db
    ports:
      - "3000:3000"

  db:
    image: mongo
```

Vamos a analizar que significan cada una de esta lineas:

| Linea  | Uso |
|  :---:  |  :----:  |
| version: "3.8"  |  Indica la version que se esta utilizando de docker compose  |
| services:  | Son los servicios o contenedores que utilizara Docker para correr los contenedores en este caso son la app y la bd |
| app: | nombre del servicio que utilizara |
| image: | la imagen que se utilizara en la app |
| enviroment:  | Declaraciòn de variables de entorno para la app |
| depends_on:  | Indica que este servicio depende de otro, en este caso app depende de db |
| ports: | el puerto que utilizara el contenedor para exponer el contenedor |
| db:  | indica la base de datos que se usara |
| image:  | Indica la imagen que utilizara la base de datos |

Ahora bien ya explicado lo anterior, vamos a cambiar algunas lineas de codigo:
- Cambiaremos la linea del servicio de _app_ porque la imagen de platziapp probablemente no este disponible, en este caso hay dos opciones posibles, podemos hacer el cambio de la imagen o podemos hacer un build en lugar de la imagen.
	- Para cambiar la imagen podemos solo hacer uso de la imagen de una app que tengamos, en este caso creamos una llamada _miap_ y modificamos la linea:
	
	```bash
	vim docker-compose.yml
	```

	```bash
	image: miapp
	```
	- La otra opcion es usar _build_ eliminamos _image:_ platziapp y agregamos _build:_ ./
	
	```bash
	build: ./
	```

Ahora bien, por el momento solo cambiamos una linea de codigo, veamos que pasa si ejecutamos con docker compose
```bash
docker compose up
```

¿Te funciono?
Posiblemente si, o posiblemente no, en mi caso si funciono, pero que pasa si intento acceder al navegador, veamos:
```bash
localhost:8080
```

Nos da un error verdad.
El error tiene que ver con el puerto que estamos accediendo, porque en nuestro archivo de _index.js_ estamos diciendo que sea en el puerto 8080, pero en docker estamos diciendo que sea en el puerto 3000, entonces manos a la obra
	- Para solucionar esto, modificaremos la linea de _ports:_
	```bash
	ports: "8080:8080"
	```

Entonces para parar el contenedor solo hacemos el uso de Ctrl + C, luego volvemos a ejecutar el siguiente comando:
```bash
docker compose up
```

Vemos que esta vez si se puede acceder desde el navegador en el puerto 8080
Pero tambien es algo molesto ver todos los logs en la terminal cuando corremos el comando de docker compose, para evitar ver todos los logs podemos hacer uso de una bandera que vimos con anterioridad:
```bash
docker compose up -d
```

El comando anterior hace que corra el contenedor sin ver los logs

__NOTA:__ Lo bueno de ver los logs es que podemos ver si falla algo al ejecutar el contenedor, en caso de no necesitarlos solo agregamos la bandera _-d_ y listo el contenedor correra sin mostrar nada.

## Subcomando de Docker Compose

__NOTA:__ Para estar utilizando los comandos de Docker Compose solo se pueden utilizar dentro de la carpeta o proyecto que contiene el archivo _docker-compose.yml_, de lo contrario si tratas de ejecutarlo en otra carpeta y no cuenta con un archivo de docker, no lo reconocera.

Vamos a ver algo interesante, sabemos que ya iniciamos el contenedor con Docker Compose, pero lo que hace Docker Compose es levantar un servio de red -> (network) lo podemos ver con comandos que ya practicamos con anterioridad:
```bash
docker network ls
```

Vemos que aparece sigue la red llamada _mired_ que nosotros creamos, pero hay algo nuevo, otra red llamada __docker_default__ esta red es la que se creo al utilizar Docker Compose, veamos que tiene esta red por dentro:
```bash
docker network inspect docker_default
```

Podemos ver que en la clave de _containers:_ hay 2  contenedores, uno es de la app y el otro de la db de mongo.

Ver los logs de los servicios

- Para ver todos los logs
```bash
docker compose logs
```

- Para ver los logs de un solo contenedor
```bash
docker compose logs <nombreServicio>
```

Ejemplo:

<pre>docker compose logs app</pre>

Te preguntaras, __¿Como saber el nombre del servicio?__, sencillo lo hacemos viendo los procesos que estan corriendo en docker
```bash
docker ps
```

Luego no fijamos en la columna de _NAMES_ (los nombres del contenedor) en mi caso aparecen como: _docker-app-1_ y _docker-db-1_ esos son los nombre de los servicios, pero para poder verlos solo agregamos la palabra que este en medio, en otras palabras, ignoramos la palabra _docker_ y el _numero_
Intentalo con el servicio de la dase de datos, te reto a ver los logs de la db

Tambien podemos hacer un _follow_ de los logs de los servicios
```bash
docker compose logs -f app
```

__NOTA:__ para salir de los logs presiona Ctrl + C

Tambien puede hacer el seguimiento de logs de varios servicios
```bash
docker compose logs app db
```

Se puede hacer con N cantidad de servicios.

Para correr un comando en un contenedor lo hacemos de la siguiente manera
```bash
docker compose exec <nombreServicio> <comandoParaEjecutar>
```

Ejemplo
<pre>docker compose exec app bash</pre>

Ahora bien, podemos listat los procesos que tiene el Docker Compose y tambien ver cuales estan corriendo

- Para listar los servicios de Docker Compose
```bash
docker compose ls
```

- Para ver los procesos activos de Docker Compose
```bash
docker compose ps
```

Ahora para poder "destruir" o parar los servicios de Docker Compose podemos hacer uso del siguiente comando:
```bash
docker compose down
```

Y mostrar los contenedores que esta destruyendo
Si nos damos cuenta y hacemos un _docker ps_ los contenedores ya no estan corriendo, lo que hizo el comando anterior es que detiene y elimina los contenedores y a su vez destruye la red donde estaban alojados _(docker_default)_ para comprobarlo verifica si la red aun esta.

## Docker Compose como herramienta de desarrollo

_docker-compose_ nació como un proyecto open source que se llamaba fig y luego docker lo compró y lo renombró como docker-compose.

Haremos algunos cambios al archivo de _docker-compose.yml_ en donde dice __image: miapp__ la modificaremos para que sea asi:
```bash
build: .
```

Luego de eso hacemos uso del siguiente comando
```bash
docker compose build
```

El cual hace referencia para que cree una imagen (hacer un build) con esa carpeta, pero ahora viene otro problema, que pasa si intento cambiar algo del codigo y quiero que se refresque de manera automatica, tocaria hacer el mismo proceso de detener el docker compose, luego iniciarlo, despues levantarlo y estar asi a cada rato, no es algo bueno, quita tiempo, entonce lo que podemos hacer es uso de los __bind mounts__ que ya vimos en capitulos anteriores.

Tendremos que agregar volumenes a _docker-compose.yml_ y el archivo queda de la siguiente manera:
```bash
version: "3.8"

services:
  app:
    build: .
    environment:
      MONGO_URL: "mongodb://db:27017/test"
    depends_on:
      - db
    ports:
      - "8080:8080"
    volumes:
      - .:/usr/src
      - /usr/src/node_modules
  db:
    image: mongo

```

¿Notas la diferencia? agregamos al servicio de _app_ volumenes para hacer que se recargue de forma automatica los cambios que hagamos en nuestra aplicacion sin necesidad de estar destruyendo y creando de nuevo el contenedor.

Ahora hacemos uso del siguiente comando para que se guarden los cambios
```bash
docker compose up -d
```

Y listo, cada cambio realizado se refrescara de manera automatica en el navegador.
Ahora en caso no te funcione hay otra solucion:
- Vamosa nuestro archivo _docker-compose.yml_ y agregamos un comando para que se ejecute en este caso es el siguiente
```bash
version: "3.8"

services:
  app:
    build: .
    environment:
      MONGO_URL: "mongodb://db:27017/test"
    depends_on:
      - db
    ports:
      - "8080:8080"
    volumes:
      - .:/usr/src
      - /usr/src/node_modules
    command: npx nodemon index.js
  db:
    image: mongo
```

Ahora para guardar los cambios lo mismo
```bash
docker compose up -d
```

Y listo ya tendria que estar todo en orden...

## Colaboracion en equipo: Override

docker-compose.override.yml es un archivo que se encarga de sobreescribir tu configuración de docker-compose.yml , se puede usar para tener segura tu configuración y para no guardar los cambios en el repositorio de git.
Un equivalente podría ser los archivos de declaración de variables de entorno, donde hay un archivo .env declarando su nombre y valor, y hay una copia .env.example con solo las variables sin valor. En .gitignore se declara que los cambios en .env no serán guardados, pero mandamos el archivo de ejemplo al repositorio.

- Crearemos un nuevo archivo en nuestra carpeta y le pondremos como nombre _docker-compse.override.yml_ este sera para agregar otras funcionalidades adicionales que querramos a nuestro proyecto
```bash

touch docker-compose.override.yml
```bash
Agregamos estas lineas de codigo
version: "3.8"

services:
  app:
    build: .
    environment:
      MY_NAME: "Daniel Noriega"
```

- Hacemos los siguiente pasos
```bash
docker compose build
```

```bash
docker compose up -d
```

```bash
docker compose exec app bash
```

El comando anterior es para algo interesante, si lo hacemos nos mandara al contenedor de Docker usando la terminal colocamos:
```bash
env
```

Y podremos ver que nos da informacion de las variables de entorno, entre ellas esta la de la base de datos y __MY_NAME__ quiere decir que ambos archivos se fusionan y toman los cambios para realizar dentro del contenedor

### Escalar contenedores en puertos en serie
Para lograr esto solo dispondremos del archivo _docker-compose.yml_ y modificaremos los puertos de esta manera:
```bash
ports:
  - "8080-8081:8080"
```

Luego hacemos uso del siguiente comando:
```bash
docker compose up -d --scale app=2
```

__NOTA:__ --scale es para que sea "escalable" entre los puertos, en caso de querer màs puertos, simplemente aumentamos el numero, ahora _app=2_ quiere decir que levantara 2 aplicaciones en los puertos que tenemos para el uso de ellos


## Administrando ambiente de Docker

Comandos para liberar espacio:
- Eliminando contenedores inactivos
```bash
docker container prune
```

- Eliminando contenedores activos e inactivos
```bash
docker rm -f $(docker ps -aq)
```

- Borrar todo lo que no estes usando (contenedores, networks, imagenes, volumenes)
```bash
docker system prune
```
Comandos para limitar memoria en un contenedor:
```bash
docker run -d --name <containerName> --memory <cantidad> <nombreImagen>
```
Por ejemplos:
<pre>docker run -d --name app --memory 1g miapp</pre>
<pre>docker run -d --name app --memory 35m miapp</pre>

__NOTA:__ El primer ejemplo uso un limite de memoria de 1 GYGA, mienras que el segundo tiene un limite de 35 Megas.

Limitar la memoria de los contenedores es util cuando queremos que varios contenedores corran al mismo tiempo y no consuman mas de lo necesario, pero hay algunos problemas en caso le coloquemos un limite de memoria menor a lo que esta consumiendo, el contenedor no funcionara, porque esa memoria no es lo suficiente para que el contenedor pueda correr.

Limitar la cantidad de CPUs en los contenedores:
Por ejemplo.
```bash
docker run --cpus=2 nginx
```

Para ver cuantos recursos esta consumiendo Docker
```bash
docker stats
```

__NOTA:__ solo funcionara cuando uno o mas contenedores esten activos.

## dockerignore

Asi como tambien existe el archivo para que git ignore carpeta o archivos, Docker implenta esto para sus contenedores. Entonces para poder hacerlo, te situas dentro de tu carpeta de donde tienes Docker para poder crear la imagen de ella.

```bash
docker build -t prueba
```

Ahora bien, ¿en que caso podemos utilizar el _.dockerignore_?
- Por ejemplo si estas haciendo una app y esta tiene dependencias instaladas de node, dentro de tu app tendras que hacer un _npm install_ y cuando realices un _docker build_ para crear la imagen, docker tambien copiara esa carpeta de _node_modules_ y sabemos que esa carpeta pesa demasiado, nunca dejaria de copiarla, entonces aca es un gran ejemplo de como podremos usar el _dockerignore_

al inicio de tu proyecto puedes crear el archivo __.dockerignore__ o en caso ya lo tengas no hay necesidad de crearlo, y dentro de esta podemos agregar las carpetas que no queremos que Docker copie a la imagen:

```bash
*.log
.dockerignore
.git
.gitignore
build/*
Dockerfile
node_modules
npm-debug.log*
README.md
```

Algo como esto prodriamos colocar, que ignore los _logs_, archivos de _git_ lo mas importante para nosotros ahora la carpeta de _node_modules_ y entre otros que no queremos que se copien dentro de la imagen para Docker

Luego de hacer esos cambios podemos crear nuestra imagen:

```bash
docker build -t ejemplo
```

Para saber si en realidad los archivos no se copiaron dentro del contenedor, accedemos a la imagen, tendremos que crear el contenedor para eso y ya sabemos como crear un contenedor y despues accedemos al contenedor:

```bash
docker run -d --rm --name micontenedor ejemplo .
```

```bash
docker exec -it micontenedor bash
```

Hacemos un _ls -la_ para ver si esos archivos se copiaron.

## Multi stage build

El Dockerfile de producción contiene 2 __“fases de build”__ que se pueden pensar como hacer 2 build seguidos, en donde al final la imagen construida contendrá lo especificado en el ultimo de los build, veamos un ejemplo de ello:

```bash
# Define una "stage" o fase llamada builder accesible para la siguiente fase
FROM node:12 as builder
# copiamos solo los archivos necesarios
COPY ["package.json", "package-lock.json", "/usr/src/"]

WORKDIR /usr/src
# Instalamos solo las dependencias para Pro definidas en package.json
RUN npm install --only=production

COPY [".", "/usr/src/"]
# instalamos dependencias de desarrollo
RUN npm install --only=development

# Pasamos los tests
RUN npm run test
## Esta imagen esta creada solo para pasar los tests.

# Productive image
FROM node:12

COPY ["package.json", "package-lock.json", "/usr/src/"]

WORKDIR /usr/src
# instar las dependencias de PRO
RUN npm install --only=production

# Copiar  el fichero de la imagen anterior.
# De cada stage se reutilizan las capas que son iguales.
COPY --from=builder ["/usr/src/index.js", "/usr/src/"]
# Pone accesible el puerto
EXPOSE 3000

CMD ["node", "index.js"]
### En tiempo de build en caso de que algún paso falle, el build se detendrá por completo.
```

- El primer build corre 1 test que verifica que todo funcione bien
- El segundo build construye la imagen final aprovechando el caché de las capas del primer build.

Al final el 2do build es solo una extracción de lo que nos interza del primer build.

Lo importante en este caso especifico es que si el _test_ falla, entonces el build 2 no se corre, lo que significa que la imagen no se construye.

Ahora, lo siguiente que haremos es contruir la imagen con los archivos de producction.Dockerfile
```bash
docker build -t <nombreImagen> <rutaApp> -f <rutaDockerfile>
```

Ejemplo
<pre>docker build -t produccion . -f build/productio.Dockerfile</pre>

Luego de esto procederemos a crear el contenedor para que se pueda ejecutar
```bash
docker run -d --name prod produccion
```

Podemos ver que nuestro contenedor esta corriendo y si nos metemos dentro del contenedor
```bash
docker exec -it prod bash
```

Y ejecutamos
```bash
ls -lac
```

Veremos que todo corrio a la perfeccion, solo se copiaron los archivos que colocamos en el archivo de _production.Dockerfile_
