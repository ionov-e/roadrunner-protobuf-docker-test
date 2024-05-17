FROM ghcr.io/roadrunner-server/roadrunner:2024.1.1 AS roadrunner
FROM php:8.3-cli-alpine3.19 as backend

RUN --mount=type=bind,from=mlocati/php-extension-installer:1.5,source=/usr/bin/install-php-extensions,target=/usr/local/bin/install-php-extensions \
     install-php-extensions opcache zip xsl dom exif intl pcntl bcmath sockets && \
     apk del --no-cache ${PHPIZE_DEPS} ${BUILD_DEPENDS}

WORKDIR /app

ENV COMPOSER_ALLOW_SUPERUSER=1
COPY --from=composer:2.3 /usr/bin/composer /usr/bin/composer

# Copy composer files from app directory to install dependencies
COPY ./composer.* .
RUN composer install --optimize-autoloader --no-dev

COPY --from=roadrunner /usr/bin/rr /usr/local/bin/rr

#EXPOSE 9001/tcp

# Copy application files
COPY ./ .

# Run RoadRunner server
CMD rr serve -c .rr.yaml