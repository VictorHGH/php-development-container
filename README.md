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

## Crear un nuevo proyecto

```bash
mkdir -p projects/www/mi_proyecto
```

Si usas framework (Laravel/Symfony/etc.), coloca su raiz dentro de `projects/www/mi_proyecto` y configura un vhost en `nginx/conf.d/sites/`.

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
