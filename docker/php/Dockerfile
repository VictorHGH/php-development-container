# ──────────────────────────────────────────────────────
# Etapa 1: imagen base PHP-FPM con extensiones necesarias
# ──────────────────────────────────────────────────────
FROM php:8.3-fpm-alpine AS php-base

# 1.1 paquetes de sistema requeridos por intl, gd, etc.
RUN apk add --no-cache icu-data-full icu-dev \
    libpng-dev jpeg-dev oniguruma-dev libzip-dev

# 1.2 compilar extensiones
RUN docker-php-ext-install \
        intl         \
        mysqli       \
        pdo_mysql    \
        gd           \
        opcache

# ──────────────────────────────────────────────────────
# Etapa 2: copiar Composer binario listo para usar
# ──────────────────────────────────────────────────────
FROM php-base AS production

# 2.1 copia /usr/bin/composer desde la imagen oficial
COPY --from=composer:2.7 /usr/bin/composer /usr/bin/composer


WORKDIR /var/www/html

CMD ["php-fpm"]
