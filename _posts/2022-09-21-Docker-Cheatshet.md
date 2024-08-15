---
layout:     post
title:      Docker Cheatsheet
date:       2022-09-21
categories: cheatsheets
---

## Introducción
* **Imagen:** como concepto básico haciendo referencia a la virtualizacion de sistemas, se puede entender como el sistema operativo o distribución que se utilizara. Solo que nos permite ir más allá, dándonos la posibilidad de utilizar _"sistemas operativos"_ diseñados para ejecutar un proyecto especifico. Por ejemplo, existe una imagen del sistema operativo `alpine (alpine:<version>)`, pero a su vez tambien existe la imagen de alpine diseñada para trabajar con node`(node:<version>-alpine)`
<br />
<br />
* **Contenedor:** paquete portable de imagenes que contienen uno o varios proyectos.

## Comandos
* Listado de imagenes descargas
```bash
docker images
```
* Descargar una imagen
```bash
docker pull <imagen>:<version>
```
* Crear un contenedor:
```bash
docker create --name <name> <imagen>
```
Devuele el id del contenedor creado
* Iniciar un contenedor
```bash
docker start <container>
```
* Parar un contenedor
```bash
docker stop <container>
```
* Listado de containers corriendo actualmente
```bash
docker ps
```
* Listado de containers creados
```bash
docker ps -a
```
* Eliminar un continer
```bash
docker rm <container>
```
* Port mapping
```bash
docker create -p<puerto-local>:<puerto-docker> --name <name> <imagen>
```
* Mostrar logs del contenedor
```bash
docker logs --follow <container> 
```
* PULL + CREATE + START
```bash
docker run --name <name> -p<puerto>:<puerto-docker> -d <imagen>
```


## Dockerfile
Se usa para especificar las acciones que realizara el container al hacer una build
* Ejemplo básico con node

```Dockerfile
FROM node:18 // <imagen>:<version>

RUN mkdir -p /home/app // crea la carpeta dentro del docker donde vamos a alojar la app
COPY . /home/app // copiamos el proyecto dentro de la carpeta que hemos creado

EXPOSE 3000  // puerto que se va exponer dentro del container
CMD ["node", "/home/app/index.js"]
```
* Para hacer una build de una imagen
```bash
docker build -t <nombre>:<version>
```

Listado de imagenes: <a href="https://hub.docker.com/search?q=" target="noopener">Docker Hub</a>

# Conexion entre contenedores
* Para listar todas las redes
```bash
docker network ls
```
* Para crear una red nueva
```bash
docker network create <name>
```
* Para configurar conexiones entre imagenes dentro de un mismo contenedor la red existente se llama igual que el controlador
* Si quieres especificar una red especifica añade la flag --network <red>

## DOCKER COMPOSE
Es una herramienta de docker para hacer todo lo anterior mucho más facil y automatizando pasos. Para ello debemos crear el fichero docker-compose.yaml.
Ejemplo de archivo docker-compose.yml con un proyecto de node y mongo:
```yaml
version: "3.9"
services: # aqui especificamos las imagenes que se incluiran en nuestra build
  node_proyect:
    build: .
    ports:
      - "3000:3000" 
    links:
      - monguito
  mongodb:
    image: mongo
    ports:
      - "27017:27017"
    environment:
      # env params
    volumes: # este parametro es importante para no perder datos cada vez que se hace una build
      - mongo-data:/data/db
volumes:
  mongo-data:
    
```

Una vez tenemos configurado este archivo, ejecutando `docker compose up` se creara un container para cada proyecto con su propia red.

# Environments
Podemos tener diferentes entornos (prod, dev) creando diferentes Dockerfiles: <br/>
Ejemplo Dockerfile.dev para proyecto con node
```Dockerfile
FROM node:18 // <imagen>:<version>

RUN npm i -g nodemon
RUN mkdir -p /home/app // crea la carpeta dentro del docker donde vamos a alojar la app

WORKDIR /home/app

EXPOSE 3000  // puerto que se va exponer dentro del container

CMD ["nodemon", "index.js"]
```

Será necesario añadir un nuevo archivo de docker-compose (docker-compose-dev.yml)
```yml
version: "3.9"
services: # aqui especificamos las imagenes que se incluiran en nuestra build
  node_proyect:
    build:
      context: .
      dockerfile: Dockerfile.dev # con este parametro incluimos un dockerfile especifico
    ports:
      - "3000:3000" 
    links:
      - monguito
    volumes:
      - .:/home/app
  mongodb:
    image: mongo
    ports:
      - "27017:27017"
    environment:
      # env params
    volumes: # este parametro es importante para no perder datos cada vez que se hace una build
      - mongo-data:/data/db
volumes:
  mongo-data:
    
```
Para ejecutar docker compose con un archivo de configuracion especifico podemos usar la flag -f: `docker compose -f docker-compose-dev.yml up`
 
Con esta info se puede empezar a cacharrear, ire actualizando conforme vaya descubriendo cosas
