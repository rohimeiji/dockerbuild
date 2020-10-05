FROM php:7.2-fpm-stretch

RUN apt-get update && apt-get install -y \
        apt-transport-https \
        gnupg2 \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update

RUN ACCEPT_EULA=Y apt-get install -y msodbcsql17

RUN apt-get install -y \
        libgss3 \
        odbcinst \
        unixodbc \
        libfreetype6 \
        libjpeg62-turbo \
        zlib1g \
        libxml2 \
        libxslt1.1 \
        libsybdb5 \
        libicu57 \
        imagemagick \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so.5 /usr/lib/libsybdb.so

RUN apt-get install -y \
        $PHPIZE_DEPS \
        unixodbc-dev \
        freetds-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        zlib1g-dev \
        libxml2-dev \
        libxslt1-dev \
        libicu-dev \
        libmagickwand-dev


RUN apt update && apt-get install telnet -y

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
        zip \
        xmlwriter \
        xsl \
        pdo_dblib \
        intl \
        sysvsem \
        sysvshm \
        sysvmsg \
    && pecl install \
        sqlsrv-5.6.1 \
        pdo_sqlsrv-5.6.1 \
        imagick-3.4.3 \
        redis-4.3.0 \
    && docker-php-ext-install pcntl \
    && docker-php-ext-enable sqlsrv \
        pdo_sqlsrv \
        imagick \
        redis \
    && rm -rf /var/lib/apt/lists/*

RUN apt update && apt-get install supervisor nano -y

RUN apt update && apt-get install -y nginx
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

EXPOSE 80/tcp
EXPOSE 9000

COPY /nginx/default.conf /etc/nginx/sites-available/default
ADD /supervisor /src/supervisor
ADD /app /app
WORKDIR /app

CMD supervisord -c /src/supervisor/supervisord.conf