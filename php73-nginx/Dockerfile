FROM php:7.3-fpm-stretch

RUN apt-get update && apt-get install -y \
        apt-transport-https \
        gnupg2 \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list && \
    apt-get -y --no-install-recommends install nano wget dialog && \
    apt-get -y --no-install-recommends install mysql-client zlib1g-dev libzip-dev && \
    apt-get -y --no-install-recommends install --fix-missing apt-utils build-essential git curl && \ 
    apt-get -y --no-install-recommends install --fix-missing libcurl3 libcurl3-dev zip openssl \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
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
        pdo_mysql \
        mysqli \
        pdo \
        mbstring \
        tokenizer \
        json \
        intl \
        gettext \
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

# Install xdebug
RUN pecl install xdebug-2.7.2 && \
    docker-php-ext-enable xdebug

# Install wkhtmltopdf
RUN apt-get update -y && apt-get install wkhtmltopdf xvfb xauth xfonts-base xfonts-75dpi fontconfig -y && \
    echo 'xvfb-run --server-args="-screen 0, 1024x768x24" /usr/bin/wkhtmltopdf $*' > /usr/bin/wkhtmltopdf.sh && \
	chmod a+rx /usr/bin/wkhtmltopdf.sh && \
	ln -s /usr/bin/wkhtmltopdf.sh /usr/local/sbin/wkhtmltopdf

# PNG Quant
RUN apt-get install -y pngquant

# OPcache
RUN docker-php-ext-install opcache

EXPOSE 80/tcp
EXPOSE 9000

COPY /nginx/default.conf /etc/nginx/sites-available/default
ADD /supervisor /src/supervisor
ADD /app /app
WORKDIR /app

CMD supervisord -c /src/supervisor/supervisord.conf