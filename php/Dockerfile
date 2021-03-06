FROM php:7.1-fpm
MAINTAINER luke <461025755@qq.com>

ENV MY_TZ=Asia/Shanghai
ENV TERM=xterm

RUN buildDeps='software-properties-common apt-utils locales tzdata' \
    && apt-get update \
    && apt-get install -y --fix-missing $buildDeps

RUN cp /usr/share/zoneinfo/$MY_TZ  /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata \
    && rm -rf /etc/timezone && echo $MY_TZ >> /etc/timezone \
    && locale-gen en_US.UTF-8 zh_CN.UTF-8 \
    && localedef -i en_US -f UTF-8 en_US.UTF-8

ENV LC_ALL=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    LANG=en_US.UTF-8 

RUN echo 'export LC_ALL=en_US.UTF-8' >> ~/.bashrc \
    && echo 'export LANG=en_US.UTF-8' >> ~/.bashrc

RUN buildDeps='libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libcurl4-openssl-dev libpng-dev imagemagick libmagickwand-dev ca-certificates build-essential memcached libmemcached-dev zlib1g-dev libxml2-dev git libxslt1-dev libicu-dev g++' \
    && apt-get update && apt-get install -y --fix-missing $buildDeps \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd opcache pdo_mysql mysqli zip soap mcrypt intl xsl \
    && echo ''| pecl install redis \
    && echo ''| pecl install memcached \
    && echo ''| pecl install imagick \
    && echo ''| pecl install swoole \
    && docker-php-ext-enable redis \
    && docker-php-ext-enable memcached \
    && docker-php-ext-enable imagick \
    && docker-php-ext-enable swoole

RUN apt-get install -y gnupg
RUN curl -OL https://phar.phpunit.de/phpunit-7.0.phar \
    && chmod +x phpunit-7.0.phar \
    && mv phpunit-7.0.phar /usr/local/bin/phpunit \
    && curl https://getcomposer.org/installer | php -- \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer \
    && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs \
    && npm -g install yarn

WORKDIR /src
RUN groupmod --gid 1000 www-data && usermod --uid 1000 --gid 1000 www-data
RUN apt-get autoclean \
    && apt-get autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
