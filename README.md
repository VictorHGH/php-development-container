# Entorno de desarrollo para proyectos PHP

Esta configuración utiliza [docker](https://www.docker.com) para la creación de un entorno de desarrollo para proyectos PHP. el conteneror utiliza php, nginx, mysql y phpmyadmin para todos los proyectos.

## Requisitos:
- Docker

Estos paquetes pueden instalarse con su gestor de paquetes favorito.

## Instalación

antes de iniciar el docker compose, se debe crear un archivo .env dentro de la carpeta mysql con el siguiente contenido:

```
MYSQL_ROOT_PASSWORD=""
MYSQL_DATABASE=""
MYSQL_USER=""
MYSQL_PASSWORD=""
```
y poner las credenciales de preferencia.

Luego de esto, se debe iniciar el docker compose con el siguiente comando:

```bash
$ docker compose up
```
### Estructura de los directorios y archivos:
``` zsh
.
├── docker-compose.yml
├── mysql
│   └── data
├── nginx
│   └── conf.d
│       └── default.conf
├── projects
│   └── www
└── README.md
```

## Como crear un proyecto nuevo

Cada proyecto se crea en un directorio dentro de la siguiente ruta:

``` zsh
$ cd projects/www
```

## Configurarar nueva proyecto en nginx

Dirigirse al directorio: nginx/conf.d

``` zsh
$ cd nginx/conf.d
```

Debido a que cada proyecto es independiente, se debe modificar el archivo default.conf para que apunte a un virtual host y a la carpeta donde se encuentra el index.php de cada proyecto. De esta manera se pueden apuntar a varios proyectos al mismo tiempo.

El siguiente código es un ejemplo de configuración para un proyecto:

Se deben modificar los valores de los campos ________ y ________ para que apunten al virtual host y la carpeta del proyecto respectivamente.

```
server {
    listen 80;
    server_name ________;

    root /var/www/html/________;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
