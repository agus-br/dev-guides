# 📦 Mini Guía: Compartir un contenedores con Docker (v1.0)

Esta guía explica varias formas de compartir un contenedor ya configurado para que pueda usarse sin tener que configurar todo desde cero.

---

## 1️⃣ Exportar e importar el contenedor completo

Puedes exportar el contenedor a un archivo `.tar` y enviarlo a otra persona.

### Exportar:
Linux
```bash
docker export container_name > container_exported_name.tar
```
Windows
```shell
docker export container_name -o C:\PATH\container_exported_name.tar
```

### Importar:
Linux
```bash
cat container_exported_name.tar | docker import - container_exported_name
```

Windows
```shell
docker import C:\PATH\container_exported_name.tar container_imported_name
```

Con esto: 
- Docker crea una nueva imagen con el nombre que le damos con el comando `import`  a partir del contenedor exportado.
- Esta imagen ya incluye todo lo que estaba dentro del contenedor en el momento de la exportación: archivos, configuraciones, backups copiados dentro del contenedor, etc.

#### ❗Importante
La imagen generada es una copia casi exacta, pero no conserva el CDM que contenía el contenedor anteriormente, no sabe que programa arrancar al ejecutarse. 
Esto se soluciona al indicarselo al correr la imagen, dejo el **ejemplo con un contenedor de mssql server**:
```bash
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=tuContraseña" -p PUERTO_HOST:1433 --name NOMBRE_CONTENEDOR -d NOMBRE_IMAGEN /opt/mssql/bin/sqlserver
```
En este caso el último parámetro indica qué programa arrancar al ejecutar el contenedor, esto solo es cuando se crea el contenedor. Cuando volvamos a ejecutar el contenedor con `docker start` ya no hará falta indicar el programa de arranque.

## 2️⃣ Crear una imagen desde tu contenedor

Esta es una de las formas más recomendadas, de esta forma es posible levantar un contenedor con una configuración base muy estable, contendrá los ajustes y cambios que hayan sicedido en el contenedor.
```bash
docker commit container_name new_image_name:tag
```

El `tag` es opcional, docker por defecto usa `:latest`,
Un ejemplo real de esto podría ser
```bash
docker commit mssql_container custom_mssqlserver_imagen:verano2025
```

Luego se puede ejecutar un contenedor como instancia de la imagen con:
```bash
docker run -OPTIONS OPTIONS-VALUES image-name:tag
```

- Todo lo que estaba dentro del contenedor (backups, archivos .mdf y .ldf copiados) se conserva.
- Más limpio y reproducible que exportar el contenedor.
- No incluye volúmenes externos montados.
- Las configuraciones como variables de entorno y opciones de puerto no se incluyen, recordemos que solo creamos una imágen a través de un contenedor.

#### ❗Importante
Las imágenes generadas terminan siendo pesadas, dependiendo de la configuración y lo que contenga el contenedor base con el que se creó.

## 3️⃣ Compartir como Dockerfile `(En revisión)*`

Es la forma más lígera y también práctica de compartir la configuración para crear un contenedor, realmente no se comparte más que una descripción de la imagen con la cual crear el contenedor.

El Dockerfile es un archivo de texto que contiene instrucciones paso a paso para construir una imagen de Docker. No tiene extención y se llama tal cual `Dockerfile`

#### 1. Crea tu Dockerfile
Ejemplo de Dockerfile para crear imagen MSSQL Server montando un archivo de backup:
```bash
FROM <imagen>
ENV <variable>
ENV <otra_variable>
```
#### ❗ Importante:
- Deberás tener tu .bak y tu Dockerfile en la misma ruta, y desde ahí abrir tu terminal para seguir con los siguientes pasos:

#### 2. Crear la imagen
El comando build crea una imagen a partir del dockerfile que esté en su alcance, su puedes elegir el nombre que tendra la imagen y también su tag `NOMBRE:TAG`.

El punto al final es para indicar que todo lo del directorio actual sea tomado por el daemon de docker, esto es útil para por ejemplo hacer que docker reconozca un archivo de configuración, dependencias o un backup para elegir que hacer con el.
```bash
docker build -t image_name .
```

#### 3. Ejecutar un contenedor
A continuación un ejemplo para correr mssql server en un contenedor acorde con el workflow que estamos siguiendo:
```bash
docker run -p HOST_PORT:CONTAINER_PORT --name container_name -d image_name
```

**❗ Importante**
Cuando se definen las variables de entorno como configuración de contraseñas estas pueden omitirse al crear el contenedor, por eso en el ejemplo no es necesario, aunque agregarlas durante el comando run es más dinámico y evitas tener que crear la imagen nuevamente si es que quieres cambiar algo. 