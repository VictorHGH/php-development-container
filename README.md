# Entorno de desarrollo PHP con Docker

Este repositorio levanta un entorno local para varios proyectos PHP usando Docker:

- Nginx (reverse proxy y virtual hosts)
- PHP-FPM 8.4 con extensiones comunes
- MySQL
- phpMyAdmin

## Requisitos

- Docker Engine + Docker Compose plugin

## Instalacion

1. Copia el archivo de ejemplo y ajusta credenciales:

```bash
cp .env.example .env
```

2. Inicia los servicios:

```bash
docker compose up -d
```

3. Accesos:

- Aplicaciones PHP: `http://localhost:8082`
- phpMyAdmin: `http://localhost:8081`

## Variables de entorno

El archivo `.env` (en la raiz del proyecto) controla credenciales y opciones de imagen.

Variables disponibles:

- `MYSQL_IMAGE` (por defecto `mysql:8.4`)
- `MYSQL_ROOT_HOST` (por defecto `%`)
- `MYSQL_ROOT_PASSWORD`
- `MYSQL_DATABASE`
- `MYSQL_USER`
- `MYSQL_PASSWORD`
- `DB_HOST` (por defecto `mysql`)

## Estructura

```text
.
├── docker-compose.yml
├── docker/
│   └── php/
│       └── Dockerfile
├── nginx/
│   └── conf.d/
│       ├── default.conf
│       └── sites/
├── projects/
│   └── www/
│       └── index.php
└── .env.example
```

## Crear un proyecto nuevo

Cada proyecto debe vivir dentro de `projects/www`:

```bash
mkdir -p projects/www/mi_proyecto
```

Luego agrega su `index.php` (o estructura del framework).

## Configurar un virtual host en Nginx

1. Crea un archivo dentro de `nginx/conf.d/sites/`, por ejemplo `mi_proyecto.test.conf`.
2. Usa esta plantilla:

```nginx
server {
    listen 80;
    server_name mi_proyecto.test;

    root /var/www/html/mi_proyecto/public;
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

3. Agrega el host en tu sistema:

```text
127.0.0.1 mi_proyecto.test
```

4. Recarga Nginx:

```bash
docker compose restart nginx
```

## Comandos utiles

- Ver logs: `docker compose logs -f`
- Parar servicios: `docker compose down`
- Reconstruir PHP: `docker compose build php`
