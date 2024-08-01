FROM ubuntu:22.04

WORKDIR /var/www/html

# MÉTADONNÉES DE L'IMAGE
LABEL maintainer="Fabrizio Manca"
LABEL version="1.0"

RUN apt update \
    && apt install sudo \
    && apt install nano

# NGINX
RUN apt install -y nginx \
    && apt install -y curl \
    && apt install net-tools \
    && apt install -y wget \
    && apt install cron \
    && apt update  \
    && apt install -y tzdata \
    && ln -fs /usr/share/zoneinfo/Europe/Paris /etc/localtime  \
    && dpkg-reconfigure --frontend noninteractive tzdata

# PHP 8.2-fpm
RUN apt update  \
    && apt install -y software-properties-common  \
    && add-apt-repository ppa:ondrej/php  \
    && apt install --no-install-recommends php8.2-fpm -y  \
    && apt install -y php8.2-curl  \
    && apt install -y php8.2-mysql  \
    && apt install -y php8.2-mbstring  \
    && apt install -y php8.2-xml  \
    && apt install -y php8.2-ftp  \
    && apt install -y php8.2-imagick  \
    && apt install -y php8.2-http  \
    && apt install -y php8.2-raphf  \
    && apt install -y php-pear  \
    && apt install -y php8.2-cli unzip \
    && apt install -y php8.2-ldap \
    && apt install -y php8.2-zip \
    && apt install -y php8.2-gd \
    && apt install php8.2-intl \
    && apt install -y php8.2-sqlite3

# OPENSSL
RUN apt update \
    && apt install -y libssl-dev \
    && apt install libcurl4-openssl-dev \
    && apt update \
    && apt install -y acl

# NODE
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - \
    &&  apt update \
    && apt install -y nodejs

# SOAP
RUN apt update \
    && apt install -y libxml2-dev \
    && apt install -y php8.2-soap

 # SUPERVISOR
RUN apt install -y supervisor

# CRON
RUN apt update  \
    && apt install cron

# COMPOSER
RUN apt install -y composer

#SQLSRV \
RUN apt update  \
    && apt install -y php8.2-dev  \
    && apt install -y unixodbc-dev  \
    && apt install -y unixodbc  \
    && apt install -y php8.2-odbc  \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -  \
    && curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list > /etc/apt/sources.list.d/mssql-release.list  \
    && apt update  \
    && ACCEPT_EULA=Y apt install -y msodbcsql17 \
    && pecl channel-update pecl.php.net  \
    && pecl install sqlsrv \
    && pecl install pdo_sqlsrv \
    && printf "; priority=20\nextension=sqlsrv.so\n" > /etc/php/8.2/fpm/conf.d/20-sqlsrv.ini  \
    && printf "; priority=30\nextension=pdo_sqlsrv.so\n" > /etc/php/8.2/fpm/conf.d/30-pdo_sqlsrv.ini \
    && phpenmod -v 8.1 sqlsrv pdo_sqlsrv

#SSH
RUN apt update  \
    && apt install -y openssh-server \
    && echo 'root:rootpsw' | chpasswd  \
    && sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config \

RUN ln -s /etc/nginx/sites-available/server /etc/nginx/sites-enabled

RUN service ssh start
RUN service php8.2-fpm start

ADD server.conf /etc/nginx/sites-available/server
ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf