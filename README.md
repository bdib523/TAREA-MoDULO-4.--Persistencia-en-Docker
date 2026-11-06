# Persistencia en Docker y depuración PHP

## Introducción

En esta práctica se trabaja con Docker para aprender a utilizar volúmenes, bind mounts y contenedores basados en PHP y Apache.

---

# 1. Volúmenes Docker

## 1.1. Crear los volúmenes

Primero creamos dos volúmenes para almacenar los datos:

```powershell
docker volume create volumen_datos
docker volume create volumen_web
```

Para comprobar que se han creado correctamente:

```powershell
docker volume ls
```

- `volumen_datos`: almacenará los datos de MariaDB.
- `volumen_web`: almacenará los archivos de la página web.

## 1.2. Crear el contenedor c1 (PHP y Apache)

Creamos un contenedor basado en la imagen `php:7.4-apache` y montamos el volumen web en la ruta `/var/www/html`.

```powershell
docker run -d --name c1 -v volumen_web:/var/www/html php:7.4-apache
```

Con este comando conseguimos que el contenedor utilice el volumen `volumen_web` para almacenar los archivos de la página web.

## 1.3. Crear el contenedor c2 (MariaDB)

Creamos un contenedor basado en la imagen MariaDB:

```powershell
docker run -d --name c2 -e MARIADB_ROOT_PASSWORD=admin -v volumen_datos:/var/lib/mysql mariadb
```

La contraseña del usuario root de MariaDB será `admin`.

El volumen `volumen_datos` se monta en `/var/lib/mysql`, que es la ubicación donde MariaDB almacena sus datos.

Para comprobar los contenedores:

```powershell
docker ps -a
```

## 1.4. Eliminar el contenedor c2 y su volumen

Primero detenemos el contenedor:

```powershell
docker stop c2
```

Después lo eliminamos:

```powershell
docker rm c2
```

Finalmente, eliminamos el volumen:

```powershell
docker volume rm volumen_datos
```

Comprobamos los volúmenes restantes:

```powershell
docker volume ls
```

---

# 2. Bind Mount con PHP y Apache

En este ejercicio utilizamos una carpeta de nuestro ordenador y la compartimos con los contenedores mediante un bind mount.

## 2.1. Crear la carpeta saludo

Creamos una carpeta llamada `saludo`:

```powershell
mkdir saludo
```

Dentro de ella creamos un archivo llamado `index.html` con el siguiente contenido:

```html
<h1>Hola soy BADR el mejor alumno</h1>
```

La estructura queda de la siguiente manera:

```text
saludo/
└── index.html
```

## 2.2. Obtener la ruta de la carpeta

Desde la carpeta principal del proyecto ejecutamos:

```powershell
$ruta = (Resolve-Path .\saludo).Path
```

Este comando obtiene la ruta completa de la carpeta `saludo`.

## 2.3. Crear el primer contenedor c1

Creamos el primer contenedor utilizando PHP y Apache:

```powershell
docker run -d --name c1 -p 8181:80 --mount "type=bind,source=$ruta,target=/var/www/html" php:7.4-apache
```

El puerto 8181 de nuestro ordenador se conecta al puerto 80 del contenedor.

La carpeta `saludo` se monta en `/var/www/html`.

Para comprobar que funciona accedemos desde el navegador:

http://localhost:8181

## 2.4. Crear el segundo contenedor c2

Creamos el segundo contenedor utilizando la misma carpeta, pero cambiando el puerto:

```powershell
docker run -d --name c2 -p 8282:80 --mount "type=bind,source=$ruta,target=/var/www/html" php:7.4-apache
```

Ahora tenemos dos contenedores que comparten los mismos archivos.

Comprobamos su funcionamiento:

```powershell
docker ps
```

Accedemos desde el navegador:

- http://localhost:8181
- http://localhost:8282

En ambos contenedores debe aparecer el contenido de `index.html`.

# 4. Comandos Docker utilizados

| Comando | Función |
|---|---|
| `docker volume create` | Crear un volumen. |
| `docker volume ls` | Mostrar los volúmenes. |
| `docker run` | Crear y arrancar un contenedor. |
| `docker ps` | Mostrar contenedores activos. |
| `docker ps -a` | Mostrar todos los contenedores. |
| `docker stop` | Detener un contenedor. |
| `docker rm` | Eliminar un contenedor. |
| `docker volume rm` | Eliminar un volumen. |
| `docker build` | Construir una imagen. |
| `docker images` | Mostrar las imágenes disponibles. |

# 5. Conclusión

Con esta práctica he aprendido a crear y administrar volúmenes Docker para mantener datos, utilizar bind mounts para compartir carpetas entre Windows y los contenedores, y configurar contenedores con PHP y Apache.

