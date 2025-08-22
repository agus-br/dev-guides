# 🚀 Guía de Comandos Docker Útiles (v1.0)

---

- [🚀 Guía de Comandos Docker Útiles (v1.0)](#-guía-de-comandos-docker-útiles-v10)
  - [📦 Imágenes](#-imágenes)
  - [🛠️ Contenedores](#️-contenedores)
  - [🔍 Inspección y estado](#-inspección-y-estado)
  - [📂 Volúmenes y datos](#-volúmenes-y-datos)
  - [🌐 Redes](#-redes)
  - [🧹 Limpieza](#-limpieza)
  - [🐳 Información del sistema](#-información-del-sistema)


## 📦 Imágenes
```bash
# Listar imágenes disponibles
docker images

# Descargar una imagen desde Docker Hub
docker pull <imagen>:<tag>

# Construir una imagen desde un Dockerfile
docker build -t <nombre_imagen>:<tag> .

# Eliminar una imagen
docker rmi <imagen_id>

# Ver historial de una imagen
docker history <imagen>
```

---

## 🛠️ Contenedores
```bash
# Crear y ejecutar un contenedor
docker run -d --name <nombre_contenedor> <imagen>

# Ejecutar un contenedor con puertos mapeados
docker run -d -p 8080:80 --name <nombre_contenedor> <imagen>

# Ejecutar un contenedor con variables de entorno
docker run -d -e VAR=valor --name <nombre_contenedor> <imagen>

# Ver logs del contenedor
docker logs -f <nombre_contenedor>

# Acceder a la terminal dentro de un contenedor
docker exec -it <nombre_contenedor> bash

# Detener un contenedor
docker stop <nombre_contenedor>

# Iniciar un contenedor detenido
docker start <nombre_contenedor>

# Reiniciar un contenedor
docker restart <nombre_contenedor>

# Eliminar un contenedor
docker rm <nombre_contenedor>
```

---

## 🔍 Inspección y estado
```bash
# Ver contenedores en ejecución
docker ps

# Ver todos los contenedores (incluye detenidos)
docker ps -a

# Ver todos los nombres de los contenedores 
docker ps -a --format "{{.Names}}"

# Ver detalles de un contenedor
docker inspect <nombre_contenedor>

# Ver procesos dentro de un contenedor
docker top <nombre_contenedor>

# Ver uso de recursos (CPU, memoria, etc.)
docker stats
```

--- 

## 📂 Volúmenes y datos
```bash
# Listar volúmenes
docker volume ls

# Crear un volumen
docker volume create <nombre_volumen>

# Montar un volumen en un contenedor
docker run -d -v <nombre_volumen>:/ruta/en/contenedor <imagen>

# Eliminar un volumen
docker volume rm <nombre_volumen>
```

--- 

## 🌐 Redes
```bash
# Listar redes
docker network ls

# Crear una red
docker network create <nombre_red>

# Conectar un contenedor a una red
docker network connect <nombre_red> <nombre_contenedor>

# Desconectar un contenedor de una red
docker network disconnect <nombre_red> <nombre_contenedor>

# Eliminar una red
docker network rm <nombre_red>
```

---  

## 🧹 Limpieza
```bash
# Eliminar contenedores detenidos
docker container prune

# Eliminar imágenes no usadas
docker image prune

# Eliminar volúmenes no usados
docker volume prune

# Eliminar todo lo no utilizado (imágenes, volúmenes, redes)
docker system prune -a
```

--- 

## 🐳 Información del sistema
```bash
# Ver información general de Docker
docker info

# Ver versión de Docker
docker version
```