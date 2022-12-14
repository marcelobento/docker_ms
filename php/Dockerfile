FROM php:5.6.40-fpm-jessie

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        curl \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        libssl-dev \
        libmcrypt-dev \
        vim

# Install the PHP mcrypt extention
RUN docker-php-ext-install mcrypt

# Install the PHP pdo_mysql extention
RUN docker-php-ext-install mysqli \
    && docker-php-ext-install pdo_mysql

#####################################
# gd:
#####################################

# Install the PHP gd library
RUN docker-php-ext-install gd && \
    docker-php-ext-configure gd \
        --enable-gd-native-ttf \
        --with-jpeg-dir=/usr/lib \
        --with-freetype-dir=/usr/include/freetype2 && \
    docker-php-ext-install gd && \
	docker-php-ext-enable gd

# Instala o SOAP
RUN rm /etc/apt/preferences.d/no-debian-php && \
    apt-get -y update && \
    apt-get -y install libxml2-dev php-soap && \
    docker-php-ext-install soap

#Habilita a extensão zip
RUN apt-get install -y \
        libzip-dev \
        zip \
  && docker-php-ext-configure zip --with-libzip \
  && docker-php-ext-install zip\
  && docker-php-ext-enable zip

RUN cd /usr/src && \
    tar -xvf php.tar.xz && \
    cp /usr/src/php-5.6.40/php.ini-development /usr/local/etc/php/php.ini

# Instala o Xdebug
RUN  apt-get install -y php5-xdebug
COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini


#####################################
# ORACLE:
#####################################
COPY ./instantclient-basic-linux.x64-12.2.0.1.0.zip /tmp
COPY ./instantclient-sdk-linux.x64-12.2.0.1.0.zip /tmp
COPY ./instantclient-sqlplus-linux.x64-12.2.0.1.0.zip /tmp
COPY oci8.ini /usr/local/etc/php/conf.d/oci8.ini
RUN apt-get install -y zip unzip libaio1 \
     && mkdir /opt/oracle \
     && cd /opt/oracle && \
     unzip /tmp/instantclient-basic-linux.x64-12.2.0.1.0.zip -d /opt/oracle && \
     unzip /tmp/instantclient-sdk-linux.x64-12.2.0.1.0.zip -d /opt/oracle && \
     ln -s /opt/oracle/instantclient_12_2/libclntsh.so.12.1 /opt/oracle/instantclient_12_2/libclntsh.so && \
     ln -s /opt/oracle/instantclient_12_2/libclntshcore.so.12.1 /opt/oracle/instantclient_12_2/libclntshcore.so && \
     ln -s /opt/oracle/instantclient_12_2/libocci.so.12.1 /opt/oracle/instantclient_12_2/libocci.so && \
     rm -rf /opt/oracle/*.zip && \
     rm -rf /tmp/*.zip && \
     echo 'instantclient,/opt/oracle/instantclient_12_2/' | pecl install oci8-2.0.12 && \
     docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_2,12.1 && \
     docker-php-ext-install pdo_oci && \
     docker-php-ext-enable oci8

#adiciona configurações customizadas
COPY custom.ini /usr/local/etc/php/conf.d/custom.ini
RUN usermod -u 1000 www-data
RUN touch /var/log/php-errors.log \
&& chmod -Rf 777 /var/log/php-errors.log

#####################################
# WKHHTML:
#####################################
COPY wkhtmltox_0.12.5-1.xenial_amd64.deb /tmp
RUN apt-get update && \
    apt-get install -y wget openssl wget xvfb libxrender1 fontconfig xfonts-75dpi && \
    wget http://nightly.odoo.com/extra/wkhtmltox-0.12.1.2_linux-jessie-amd64.deb && \
    dpkg -i wkhtmltox-0.12.1.2_linux-jessie-amd64.deb

RUN cp /usr/local/bin/wkhtmltopdf /usr/bin && \
    cp /usr/local/bin/wkhtmltoimage /usr/bin

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000