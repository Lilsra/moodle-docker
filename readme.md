# Despliegue de Moodle con Docker

Este proyecto muestra cómo desplegar Moodle utilizando Docker y Docker Compose.

## Requisitos

- Docker instalado
- Docker Compose instalado
- Git instalado
- Conexión a internet

## Crear Carpeta Moodle

Creammos una carpeta llamada Moodle y luego entramos en ella
```bash
mkdir Moodle
cd Moodle
```
## Crear Dockerfile

Dentro de la carpeta Moodle creammos un Dockerfile con lo siguiente
```bash
FROM php:8.3-apache

RUN apt-get update && \
 apt-get install -y --no-install-recommends unzip git curl libzip-dev libjpeg-dev libpng-dev libfreetype6-dev libicu-dev libxml2-dev

RUN docker-php-ext-configure gd --with-freetype --with-jpeg && \
 docker-php-ext-install mysqli zip gd intl soap exif && \
 docker-php-ext-enable mysqli zip gd intl soap exif

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN git clone git://git.moodle.org/moodle.git && \
 cd moodle && \
 git branch --track MOODLE_405_STABLE origin/MOODLE_405_STABLE && \
 git checkout MOODLE_405_STABLE && \
 cp -rf ./* /var/www/html/

RUN echo "max_input_vars=5000" >> /usr/local/etc/php/conf.d/docker-php-moodle.ini && \
 echo "opcache.enable=1" >> /usr/local/etc/php/conf.d/docker-php-moodle.ini

RUN mkdir -p /var/www/moodledata

WORKDIR /var/www/html

RUN chown -R www-data:www-data /var/www/ && \
 chmod -R 755 /var/www

EXPOSE 80
```
## Crear docker-compose
Creammos un documento docker-compose.yml
```bash
services:
 moodleapp:
  build:
   context: .
  image: moodleapp:1.0
  container_name: moodleapp
  ports:
   - 80:80
  volumes:
   - moodledata:/var/www/moodledata
  depends_on:
   - moodledb

 moodledb:
  image: mysql:8.0.36
  container_name: clouddb
  environment:
   MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
   MYSQL_DATABASE: ${MYSQL_DATABASE}
   MYSQL_USER: ${MYSQL_USER}
   MYSQL_PASSWORD: ${MYSQL_PASSWORD}
  volumes:
   - clouddbdata:/var/lib/mysql

 phpmyadmin:
  image: phpmyadmin
  container_name: phpmyadmin
  restart: always
  ports:
   - "8081:80"
  environment:
   PMA_HOST: clouddb
   PMA_PORT: 3306
   PMA_USER: ${MYSQL_USER}
   PMA_PASSWORD: ${MYSQL_PASSWORD}
  depends_on:
   - moodledb

volumes:
 clouddbdata:
 moodledata:
```
## Crear archivo env

Creammos un archivo .env
```bash
MYSQL_ROOT_PASSWORD=mysql-rootpass
MYSQL_DATABASE=moodlelms
MYSQL_USER=moodle-admin
MYSQL_PASSWORD=moodle-dbpass
PMA_PASSWORD=moodle-dbpass
PMA_USER=moodle-admin
```
## Clonar el repositorio
1) Para iniciar con el proceso en otros dispositivos, primero colonaremos el repositorio al dispositivo que usaremos
```bash
git clone https://github.com/TU_USUARIO/moodle-docker.git
cd moodle-docker
```
2) En este caso usaremos 
```bash
https://github.com/Lilsra/moodle-docker.git
```
## Comandos para el uso de Docker

una vez ya hecho todo lo anterior usamos dentro de la terminal el comando
```bash
docker compose up -d
```
o en caso de que se quiera observar toda la ejecucion del server
```bash
docker compose up
```
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/189fed0e-2c19-42cf-bd31-d590860c99ef" />


## Iniciar Moodle en el navegador
usado sustituyendo los 0.0.0.0 por tu direccion ip para iniciar en el moodle 
```bash
0.0.0.0:80 
```
seleccionamos el lenguaje
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/4b356b6d-45d0-4031-8b92-b4fcfcd9be48" />

proceso de instalacion 
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/95821c21-4d38-4f61-b717-f16745115260" />


seleccionamos la base de datos que usaremos
<img width="880" height="441" alt="image" src="https://github.com/user-attachments/assets/5bc7ba8d-d608-432d-a9f6-e1e117fca5ef" />

introducimos la informacion para poder acceder a la base de datos
<img width="943" height="564" alt="image" src="https://github.com/user-attachments/assets/9f4c74fd-a15b-4284-a0cf-e6f975ab2dd7" />

en breve aparecera una pantalla de instalacion, simplemente le damos aceptar y seguimos con la espera de la verificacion de actualizaciones y pluggins
aceptamos y ya estara listo el MSL.

## Configurando moodle 

configuramos el administrador 
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/fd2b2c1d-0ed5-4009-9561-82bd286d8c59" />

creamos curso de ejemplo 
<img width="1902" height="802" alt="image" src="https://github.com/user-attachments/assets/2b6a0be9-aa94-431d-9dd2-0062b3c98547" />



