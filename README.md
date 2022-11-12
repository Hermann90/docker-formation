# Docker formation By Hermann90
## _Le processus de formation a la techno Docker_

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Dans ce depot, nous allons decrire les etapes pour apprendre les bases de la tecchno Docker.
- ✨Magic ✨

## Environement de test : Vagrant, virtualBox
### prerequis : 
- Install VirtualBox
- Install Vagrant
- Visual Studio Code
##### Installation de Docker dans un serveur virtuel centos 7:
- cloner ce depot, puis suivez les etapes ci-dessous : 
```sh
cd centos_os/
vagrant up
```
- Connectez vous par ssh au serveur qui est demarrer.

##### Docker 1 : Some basic docker command
## _docker ps : see the started containers_
## _docker run -di --name hermann alpine:latest : run the container to detach mode and interractive_
## _docker exec -ti  hermann sh: connect to the container_

```sh
docker ps
docker ps -a
docker pull alpine:latest
docker run -di --name hermann alpine:latest
docker exec -ti hermann sh
```

##### Docker 2 : Start a container Ex. NGINX 
## _docker run -tid -p 8080:80 --name web nginx:latest : run the ngnix server, expose to 8080 port from localhost. in the container we have 80 port open for the nginx server. open the browser and make 127.0.0.1:8080 to see the nginx started page_
## _docker inspect web : display all informations about the web container started_

```sh
docker run -tid -p 8080:80 --name web nginx:latest
docker inspect web
docker stop web
```

##### Docker 3 : Persistant Volume : 
1. start the web container created with nginx : 
2. connect to this container using exec docker command :
- update this container
- install vi or vim in this container
- edit the /usr/share/nginx/html/index.html then put your name, save and quit
- refresh your browser : you can see your change
- quit to the iterrative mode
- stop the container, and refresh the browser : nothing to display because the container is down
- restart it, then refresh the browser : "welcome hermann 90! .." message displayed
- delete the container, than restart another one. Refresh your browser : you are loose all modification
##### Note : the containers don't store the data. to solve this problem, you can create the volume to store data, them map this volume when you're create container process. 

3. create this folder in you're host machine : /srv/data/nginx
4. delete the web container
5. create the web container again, using the -v option to map volume  (-v /srv/data/ngnix:/usr/share/ngnix/html/)
6. refresh the browser : error web page (403 Forbidden)
7. create the index.html page in your /srv/data/nginx host volume, then put in this file some text or html code
- html example code to put in index.html
```html
<!DOCTYPE html>
<html>
<body>
<h1>My First We Page </h1>
<p>My first paragraph.</p>
</body>
</html>
```
8. refresh the browser: your text or html code result can displayed
9. delete the container, then refresh the browser : nothing to display
10. create again th web container by maping the same volume
10. refresh the browser : you change is dispaly

```sh
docker start web
docker exec -it web sh
apt-get update
apt-get install vim -y
vim /usr/share/nginx/html/index.html
#edit the welcom message and put your name (Welcome to Hermann 90), then save and quit and refresh browser
exit
docker stop web
docker start web
docker rm -f web
docker ps -a
docker run -dit -p 8080:80 --name web nginx
sudo mkdir -p /srv/data/nginx
docker rm -f web
docker run -dit -p 8080:80 --name web -v /srv/data/nginx:/usr/share/nginx/html/ nginx
sudo vim /srv/data/nginx/index.html #copy and paste the html code above in this file
docker rm -f web
docker run -dit -p 8080:80 --name web -v /srv/data/nginx/:/usr/share/nginx/html nginx
```