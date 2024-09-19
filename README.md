# Docker Proxy
Creamos un contenedor docker con el comando docker run y le asignamos el nombre con *--name* seguido del nombre que le queramos asignar. Con *-d* le indicamos que se ejecute en modo detached, esto significa que el contenedor se estara ejecutando en segundo plano, sin necesidad de mantener la terminal abierta. Mapeamos los puertos con *-p* seguido del puerto local escogido y el puerto del contenedor donde se hara el mapeo. Finalmente se especifica la imagen.
``` Batchfile
C:\Users\Pc-Gamer> docker run --name dockerproxy -d -p 8080:80 nginx
```
Realizamos la prueba para comprobar que nginx ya esta en funcionamiento en [127.0.0.1:8080](http://127.0.0.1:8080)
![image](https://github.com/user-attachments/assets/632c3f40-705f-4919-8d30-83224b877993)

Para ver que las imagenes que hemos creado, usamos el siguiente comando, donde con *ps -a* le decimos que muestre la lista de contenedores y *-a* (all) para que muestre todas las imagenes incluso las que no esten activas.
``` Batchfile
C:\Users\Pc-Gamer> docker ps -a
```
![image](https://github.com/user-attachments/assets/87e2cce7-56da-4f3a-8112-db30b94bb824)

Lo siguiente es abrir el shell bash para interactuar con la imagen que acabamos de crear, ponemos *-i* para señalar que vamos a interactuar con la terminal de la imagen. *-t*  permite que la terminal del contenedor se comporte de manera similar a una terminal local, ponemos el id del contedor al que queremos acceder y por ultimo el comando que se ejecutara dentro del contendor.
``` Batchfile
C:\Users\Pc-Gamer> docker exec -it 197ae79943db bash
```


Agregamos a un usuario y rellenamos todos los datos solicitados.
``` Batchfile
root@197ae79943db:/# adduser dev
```
![image](https://github.com/user-attachments/assets/eed15f59-4344-4423-b751-6ef26336b8bc)

Actualizamos el sistema e instalamos python3.11 junto a su entorno virtual, pip y vim.
``` Batchfile
root@197ae79943db:/# apt update
root@197ae79943db:/# apt install -y python3.11
root@197ae79943db:/# apt install -y python3.11-venv
root@197ae79943db:/# apt install -y python3-pip
root@197ae79943db:/# apt install -y vim
```
Dentro del usuario creamos el entorno virtual de python
``` Batchfile
root@197ae79943db:/# su dev
dev@197ae79943db:/$ cd home
dev@197ae79943db:/home$ ls
dev
dev@197ae79943db:/home$ cd dev/
dev@197ae79943db:~$ python3 -m venv devsenv
dev@197ae79943db:~$ source devsenv/bin/activate
```
Creamos un archivo de prueba
``` Batchfile
(devsenv) dev@197ae79943db:~$ vim test.py
```
Creamos la aplicacion de flask
``` python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hola Mundo :D</p>"
```
Instalamos flask en el entorno virtual (venv), salimos del venv y salimos del usuario
``` Batchfile
(devsenv) dev@197ae79943db:~$ pip install flask
(devsenv) dev@197ae79943db:~$ deactivate
dev@197ae79943db:~$ exit
```
Buscamos el archivo default.conf y lo abrimos con vim
``` Batchfile
root@197ae79943db:/# cd /
root@197ae79943db:/# cd etc/nginx/conf.d/
root@197ae79943db:/etc/nginx/conf.d# ls
default.conf
root@197ae79943db:/etc/nginx/conf.d# vim default.conf
```

Dentro de default.conf agrgamos una localizacion

![image](https://github.com/user-attachments/assets/bf910c1e-d2e4-4529-8d75-f36a49b0c70d)

De nuevo entramos al usuario y activamos el venv para despues editar el **test.py**
Entrar al usuario, navegar al directorio mostrado, activar venv y editar el archivo creado anteriormente
``` Batchfile
root@197ae79943db:/etc/nginx/conf.d# su dev
dev@197ae79943db:/etc/nginx/conf.d$ cd /
dev@197ae79943db:/$ cd home
dev@197ae79943db:/home$ cd dev
dev@197ae79943db:~$ source devsenv/bin/activate
(devsenv) dev@197ae79943db:~$ vim test.py
```

Agregamos la direccion a nuestra app
``` python
from flask import Flask

app = Flask(__name__)

@app.route("/server")
def hello_world():
    return "<p>Hola Mundo :D</p>"
```
Desactivamos el venv , salimos del usuario para recargar nginx y de nuevo ingresar al venv 
``` Batchfile
(devsenv) dev@197ae79943db:~$ deactivate
dev@197ae79943db:~$ exit
root@197ae79943db:/# nginx -s reload
root@197ae79943db:/# su dev
dev@197ae79943db:/$ cd home/dev/
dev@197ae79943db:~$ source devsenv/bin/activate
```
Corremos el archivo test con flask
``` Batchfile
(devsenv) dev@197ae79943db:~$ flask --app test run
```
![image](https://github.com/user-attachments/assets/3fa56f49-73b5-41a4-ad29-ec2dbea63def)
