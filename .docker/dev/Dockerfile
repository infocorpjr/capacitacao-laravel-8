FROM php:7.4-fpm-alpine

LABEL Jorge Rodrigues <jorgerodrigues9@outlook.com>

# Add Repositories
#RUN rm -f /etc/apk/repositories &&\
#    echo "http://dl-cdn.alpinelinux.org/alpine/main" >> /etc/apk/repositories && \
#    echo "http://dl-cdn.alpinelinux.org/alpine/community" >> /etc/apk/repositories

# Add Build Dependencies
RUN apk add --no-cache --virtual .build-deps \
    icu-dev \
    libxml2-dev \
    freetype-dev \
    zlib-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    libwebp-dev \
    libzip-dev \
    #libonig-dev \
    bzip2-dev \
    curl-dev \
    nodejs \
    npm

# Add Production Dependencies
RUN apk add --update --no-cache \
    supervisor


# Configure & Install Extension
RUN docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/

RUN docker-php-ext-install \
    mysqli \
    pdo \
    pdo_mysql \
    sockets \
    json \
    gd \
    xml \
    zip \
    curl

# Add Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH="./vendor/bin:$PATH"

# Setup Crond and Supervisor by default
RUN echo '*  *  *  *  * /usr/local/bin/php  /var/www/artisan schedule:run >> /dev/null 2>&1' > /etc/crontabs/root

# Setup PHP
COPY .docker/dev/php/opcache.ini $PHP_INI_DIR/conf.d/
COPY .docker/dev/php/php.ini $PHP_INI_DIR/conf.d/

# Setup Supervisor
RUN mkdir /etc/supervisor.d
ADD .docker/dev/supervisor/supervisor.ini /etc/supervisor.d/

# Remove Build Dependencies
# RUN apk del -f .build-deps

# Setup Working Dir
WORKDIR /var/www

#COPY . .
#COPY .env.development .env

#RUN composer install && \
#    php artisan route:clear && \
#    php artisan view:clear && \
#    php artisan config:clear && \
#    php artisan cache:clear

#RUN npm ci && \
#    npm run prod

# Expose port 9000 and start php-fpm server
EXPOSE 9000

CMD ["/usr/bin/supervisord"]
