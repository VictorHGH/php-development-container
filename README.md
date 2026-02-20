# Entorno PHP con Docker

Repositorio base para desarrollo de multiples proyectos PHP en un solo stack local.

## Servicios

- Nginx (`127.0.0.1:8082`)
- PHP-FPM 8.4 con extensiones comunes
- MySQL (volumen persistente `mysql_data`)
- phpMyAdmin (`127.0.0.1:8081`)

## Estructura

- `docker-compose.yml`: orquestacion principal.
- `docker/php/Dockerfile`: imagen PHP personalizada.
- `nginx/conf.d/`: configuracion Nginx y virtual hosts.
- `projects/www/`: raiz donde se alojan los proyectos PHP.
- `.env.example`: variables de entorno para DB e imagen MySQL.

## Requisitos

- Docker + Docker Compose plugin.

## Inicio rapido

1. Crear archivo de entorno:

```bash
cp .env.example .env
```

2. Levantar stack:

```bash
docker compose up -d
```

3. Acceder:

- Apps PHP: `http://127.0.0.1:8082`
- phpMyAdmin: `http://127.0.0.1:8081`

## Agregar un proyecto completo

Guia recomendada para dejar un proyecto listo (codigo + dominio local + Nginx + DB opcional).

1. Crear carpeta del proyecto:

```bash
mkdir -p projects/www/mi_proyecto
```

2. Copiar el codigo dentro de `projects/www/mi_proyecto`.

3. Crear un vhost en `nginx/conf.d/sites/mi_proyecto.test.conf`:

```nginx
server {
    listen 80;
    server_name mi_proyecto.test;

    # PHP puro:
    root /var/www/html/mi_proyecto;
    # Framework (Laravel/Symfony): usa /public
    # root /var/www/html/mi_proyecto/public;

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

4. Registrar el dominio local en `/etc/hosts`:

```text
127.0.0.1 mi_proyecto.test
```

5. Recargar Nginx y validar:

```bash
docker compose restart nginx
```

Abrir: `http://mi_proyecto.test:8082`

6. (Opcional) Instalar dependencias PHP con Composer dentro del contenedor:

```bash
docker compose exec php composer install -d /var/www/html/mi_proyecto
```

7. (Opcional) Crear una base de datos dedicada para el proyecto:

```bash
docker compose exec mysql sh -lc 'mysql -u root -p"$MYSQL_ROOT_PASSWORD" -e "CREATE DATABASE mi_proyecto_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"'
```

8. (Opcional) Importar un dump SQL:

```bash
docker compose exec -T mysql sh -lc 'mysql -u root -p"$MYSQL_ROOT_PASSWORD" mi_proyecto_db' < backup.sql
```

## Variables principales (`.env`)

- `MYSQL_IMAGE`
- `MYSQL_ROOT_HOST`
- `MYSQL_ROOT_PASSWORD`
- `MYSQL_DATABASE`
- `MYSQL_USER`
- `MYSQL_PASSWORD`
- `DB_HOST`

## Operacion diaria

```bash
docker compose ps
docker compose logs -f
docker compose restart nginx
docker compose down
```

## Notas

- Los proyectos y la configuracion Nginx se montan por volumen, por lo que los cambios aplican en caliente.
- El volumen `mysql_data` conserva datos entre reinicios.
