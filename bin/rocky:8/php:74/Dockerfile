FROM rockylinux:8

# Update
RUN dnf -y update && \
    dnf upgrade -y

# Install useful tools and install important libaries
RUN dnf -y update && dnf -y install wget nano dialog sqlite-libs \
     mysql zlib libzip libicu git curl libcurl openssl zip make \
     gcc gcc-c++ autoconf automake vim libzip mod_fcgid mod_ssl --skip-broken

# Install and start Apache Web Server
RUN dnf -y install httpd
RUN sed -i "s/#ServerName /ServerName www.example.com:80/" "/etc/httpd/conf/httpd.conf"
RUN systemctl enable httpd

# Install PHP and its extensions
RUN dnf -y module reset php
RUN dnf -y module install php:7.4
RUN dnf -y update && dnf -y install php-common php-curl \
    php-pdo php-mysqlnd php-json php-mbstring php-soap php-xml php-gd \
    php-bcmath php-cli php-pear php-devel php-intl
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN sed -i "s/listen.acl_users/;listen.acl_users/" "/etc/php-fpm.d/www.conf"
RUN sed -i "s/;listen.owner = nobody/listen.owner = apache/g" "/etc/php-fpm.d/www.conf"
RUN sed -i "s/;listen.group = nobody/listen.group = apache/g" "/etc/php-fpm.d/www.conf"
RUN systemctl enable php-fpm

# Install xdebug
RUN pecl install xdebug-2.8.0
RUN PHPINIFILE=`find /etc -name php.ini -print` && \
    echo '' >> "$PHPINIFILE" && \
    echo '[xdebug]' >> "$PHPINIFILE" && \
    echo 'zend_extension="/usr/lib64/php/modules/xdebug.so"' >> "$PHPINIFILE" && \
    echo 'xdebug.remote_enable = 1' >> "$PHPINIFILE" && \
    echo '' >> "$PHPINIFILE"

# Customize php.ini
RUN PHPINIFILE=`find /etc -name php.ini -print` && \
    sed -i "s/;date.timezone =/date.timezone = UTC/" "$PHPINIFILE" && \
    sed -i "s/;display_errors = On/display_errors = On/" "$PHPINIFILE" && \
    sed -i "s/;log_errors = On/log_errors = On/" "$PHPINIFILE" && \
    sed -i "s/short_open_tag = Off/short_open_tag = On/" "$PHPINIFILE" && \
    sed -i "s/;session.save_path = \/tmp/session.save_path = \/tmp/" "$PHPINIFILE"

# Install imagick
RUN dnf -y update && \
    dnf -y install epel-release && \
    dnf -y install ImageMagick ImageMagick-devel --skip-broken --allowerasing

EXPOSE 80
ENTRYPOINT ["/usr/sbin/init"]