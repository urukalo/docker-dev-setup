FROM php:7.1-fpm-alpine

LABEL maintainer Srdjan Vranac <vranac@gmail.com>

# persistent / runtime deps
ENV PHPIZE_DEPS \
    autoconf \
    cmake \
    file \
    g++ \
    gcc \
    libc-dev \
    make \
    git \
    pkgconf \
    re2c

# https://pkgs.alpinelinux.org/packages?name=&branch=&repo=&arch=&maintainer=
RUN echo http://dl-4.alpinelinux.org/alpine/v3.3/main/ >>/etc/apk/repositories \
    && echo http://dl-4.alpinelinux.org/alpine/v3.3/community/ >>/etc/apk/repositories \
    && echo http://dl-4.alpinelinux.org/alpine/v3.4/main/ >>/etc/apk/repositories \
    && echo http://dl-4.alpinelinux.org/alpine/v3.4/community/ >>/etc/apk/repositories \
    && echo http://dl-4.alpinelinux.org/alpine/v3.5/main/ >>/etc/apk/repositories \
    && echo http://dl-4.alpinelinux.org/alpine/v3.5/community/ >>/etc/apk/repositories \
    && echo http://dl-4.alpinelinux.org/alpine/edge/community/ >>/etc/apk/repositories \
    && apk update \
    && apk add --no-cache --virtual .build-deps \
            $PHPIZE_DEPS \
    && apk add git \
            bash \
            curl \
            icu \
            icu-libs \
            icu-dev \
            postgresql-dev \
            icu-dev \
            freetype \
            libpng \
            libjpeg-turbo \
            freetype-dev \
            libpng-dev \
            libjpeg-turbo-dev \
            libxslt-dev \
            libxml2-dev \
            imagemagick \
            imagemagick-dev \
            libtool \
    && pecl install xdebug-2.5.0 redis-3.1.1 imagick-3.4.3 \
    && docker-php-ext-enable redis xdebug imagick \
    && docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-configure intl \
    && docker-php-ext-install -j9 \
        intl \
        iconv \
        pdo_mysql \
        pdo_pgsql \
        exif \
        bcmath \
        pcntl \
        opcache \
        gd \
        xsl \
    && rm -rf /var/cache/apk/* \
    # Install Composer
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    # Install Symfony installer
    && curl -LsS https://symfony.com/installer -o /usr/local/bin/symfony \
    && chmod a+x /usr/local/bin/symfony

ADD ./php-fpm.conf /etc/php-fpm/php-fpm.conf

ADD ./php-fpm/ /usr/local/etc/php/conf.d/

ADD ./php-xdebug/ /usr/local/etc/php/conf.d/

RUN mkdir -p /usr/share/php-fpm
RUN mkdir -p /var/log/php-fpm
RUN mkdir -p /var/log/xdebug
RUN mkdir -p /var/log/xdebug/profiler_output

EXPOSE 9002

CMD ["php-fpm", "--allow-to-run-as-root", "--fpm-config", "/etc/php-fpm/php-fpm.conf"]
