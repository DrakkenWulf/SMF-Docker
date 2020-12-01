FROM php:7.3-fpm
COPY smf/ .
USER root
RUN chmod +x /var/www/html
# This command set isn't happening for some reason, has to be done manually?
RUN cp other/install.php install.php && \
    cp other/install_2-1_mysql.sql install_2-1_mysql.sql && \
    cp other/Settings.php Settings.php && \
    chmod 777 Settings.php && \
    cp Settings.php Settings_bak.php && \
    chmod 777 Settings_bak.php
RUN apt-get update && apt-get install -y --no-install-recommends \
        sendmail \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) mysqli

# tell php about sendmail
RUN echo "sendmail_path=/usr/sbin/sendmail -t -i" >> /usr/local/etc/php/conf.d/sendmail.ini
# start sendmail when php starts
RUN sed -i '/#!\/bin\/sh/aservice sendmail restart' /usr/local/bin/docker-php-entrypoint
# link up hostnames so this internal sendmail can know our public address
RUN sed -i '/#!\/bin\/sh/aecho "$(hostname -i)\t$(hostname) $(hostname).localhost hope.coldfire.org" >> /etc/hosts' /usr/local/bin/docker-php-entrypoint


ARG WITH_XDEBUG=true

RUN if [ $WITH_XDEBUG = "true" ] ; then \
        pecl install xdebug; \
        docker-php-ext-enable xdebug; \
        echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
        echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
        echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
        echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini; \
    fi ;