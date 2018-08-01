FROM php:5.6-apache
ARG POST_MAX_SIZE
ARG UPLOAD_MAX_FILESIZE
ARG TIMEZONE

RUN truncate -s 0 /etc/apt/sources.list

RUN printf "deb http://ftp.pl.debian.org/debian/ jessie main contrib non-free" >> /etc/apt/sources.list.d/stable.list
RUN printf "deb http://ftp.pl.debian.org/debian/ jessie-updates main contrib non-free" >> /etc/apt/sources.list.d/updates.list
RUN printf "deb http://security.debian.org jessie/updates main contrib non-free" >> /etc/apt/sources.list.d/security.list

RUN printf "deb http://httpredir.debian.org/debian jessie-backports main contrib non-free\ndeb-src http://httpredir.debian.org/debian jessie-backports main contrib non-free" > /etc/apt/sources.list.d/backports.list

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN apt-get update && apt-get install -y \
	git \
	unzip \
        python-setuptools \
	libfdk-aac-dev \
	libx264-dev \
	wkhtmltopdf \
        yasm

RUN printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini
RUN printf "[PHP]\nupload_max_filesize = %s\n" "${UPLOAD_MAX_FILESIZE}" > /usr/local/etc/php/conf.d/upload_max_filesize.ini
RUN printf "[PHP]\npost_max_size = %s\n" "${POST_MAX_SIZE}" > /usr/local/etc/php/conf.d/post_max_size.ini

RUN rm -rf ./app/cache/*
RUN rm -rf ./app/logs/*

RUN chmod -R 777 /var/www/html/

RUN a2enmod rewrite
ADD myapp.conf /etc/apache2/sites-available/000-default.conf
ADD php.ini /usr/local/etc/php/php.ini

RUN docker-php-ext-install pdo pdo_mysql pcntl

RUN easy_install supervisor

RUN mkdir /var/log/supervisor
RUN touch /var/log/supervisor/supervisord.log

WORKDIR /var/www/html
