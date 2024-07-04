# Use the official Ubuntu base image
FROM ubuntu:jammy

# Set timezone and make installation non-interactive
ENV TZ=Asia/Kolkata
ENV DEBIAN_FRONTEND=noninteractive

# Update packages and install necessary packages and OpenLiteSpeed
RUN apt-get update && \
    apt-get install -y \
        software-properties-common \
        wget \
        git \
        curl \
        unzip \
        nano \
        zip && \
    wget -O - https://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh | bash && \
    apt-get install -y \
        openlitespeed \
        lsphp82 lsphp82-mysql lsphp82-common lsphp82-opcache lsphp82-intl lsphp82-curl lsphp82-imap lsphp82-ldap lsphp82-redis lsphp82-memcached && \
    rm -rf /var/lib/apt/lists/*

# Configure PHP settings
RUN sed -i -e 's/^file_uploads =.*/file_uploads = On/' \
           -e 's/^allow_url_fopen =.*/allow_url_fopen = On/' \
           -e 's/^short_open_tag =.*/short_open_tag = On/' \
           -e 's/^memory_limit =.*/memory_limit = 256M/' \
           -e 's/^;cgi.fix_pathinfo=1.*/cgi.fix_pathinfo = 0/' \
           -e 's/^upload_max_filesize =.*/upload_max_filesize = 100M/' \
           -e 's/^post_max_size =.*/post_max_size = 100M/' \
           -e 's/^max_execution_time =.*/max_execution_time = 360/' \
           /usr/local/lsws/lsphp82/etc/php/8.2/litespeed/php.ini

# Create virtual host configuration directory
RUN mkdir -p /usr/local/lsws/conf/vhosts/tamil.localhost

# Copy virtual host configuration file
COPY vhconf.conf /usr/local/lsws/conf/vhosts/tamil.localhost/vhconf.conf

# Create document root directory and set permissions
RUN mkdir -p /var/www/html && \
    chown -R nobody:nogroup /var/www/html

# Copy index.php file to document root
COPY index.php /var/www/html/index.php

# Expose ports (80 for HTTP)
EXPOSE 80

# Set entrypoint to OpenLiteSpeed service
CMD ["/usr/local/lsws/bin/lswsctrl", "start"]
