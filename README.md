# Entorno de desarrollo para proyectos PHP

Esta configuración utiliza [podman](https://podman.io/) y [docker-compose](https://docs.docker.com/compose/) para la creación de un entorno de desarrollo para proyectos PHP. el conteneror utiliza php, nginx, mysql y phpmyadmin para todos los proyectos.

## Requisitos:
- podman
- docker-compose

Estos paquetes pueden instalarse con su gestor de paquetes favorito.

## Instalación

```bash
$ podman compose -f container-compose.yml up -d
```

### Estructura de los directorios y archivos:
.
├── container-compose.yml
├── mysql
│   └── data
├── nginx
│   └── conf.d
│       └── default.conf
├── projects
│   └── www
└── README.md

## Como crear un proyecto nuevo

Cada proyecto se crea en un directorio dentro de la siguiente ruta:

```
$ cd projects/www
```

## Configurarar nueva proyecto en nginx

Dentro del archivo de configuración que se encuentra en la ruta:

```
$ cd nginx/conf.d
```

Editar y agregar el siguiente contenido:

```
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```
