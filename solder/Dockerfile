FROM alpine:latest

WORKDIR /var/www/

# Download and install dependencies
RUN apk --no-cache add wget
# Download and install Solder dependencies
RUN apk --no-cache add composer php7-cli php7-curl php7-mcrypt php7-apcu php7-fpm php7-sqlite3 php7-xml php7-fileinfo \
                       php7-dom php7-tokenizer php7-ctype php7-pdo php7-pgsql php7-pdo_pgsql php7-gd git

# Download and extract solder
RUN git clone -b dev https://github.com/BangRocket/TechnicSolder.git
# Better file naming
RUN mv ./TechnicSolder ./technicsolder

# Create default configs.
RUN mv ./technicsolder/app/config-sample ./technicsolder/app/config

# Changed working directory so composer can work without trouble
WORKDIR /var/www/technicsolder/

# Install solder dependencies.
RUN composer install --no-dev --no-interaction  --prefer-dist --no-ansi --no-scripts --optimize-autoloader
RUN composer clearcache

# Copy scripts
COPY ./entrypoint.sh .
COPY ./php_config.sh .

# Configure php-fpm
#RUN sh ./php_config.sh

RUN sed -i.bak "s|;error_log\s*=\s*log/php7/error.log|error_log = /proc/self/fd/2|g" /etc/php7/php-fpm.conf
RUN sed -i.bak "s|user\s*=\s*nobody|user = www-data|g" /etc/php7/php-fpm.d/www.conf
RUN sed -i.bak "s|group\s*=\s*nobody|group = www-data|g" /etc/php7/php-fpm.d/www.conf
RUN sed -i.bak "s|;*listen\s*=\s*127.0.0.1:9000|listen = 0.0.0.0:9000|g" /etc/php7/php-fpm.d/www.conf
RUN sed -i.bak "s|;*listen.owner\s*=\s*nobody|listen.owner = www-data|g" /etc/php7/php-fpm.d/www.conf
RUN sed -i.bak "s|;*listen.group\s*=\s*nobody|listen.group = www-data|g" /etc/php7/php-fpm.d/www.conf

# create the app user
RUN addgroup -S www-data && adduser -S www-data -G www-data

# Change permisions.
RUN chown -R www-data:www-data .

# change to the app user
USER www-data

# Actually configure solder
ENTRYPOINT ["/var/www/technicsolder/entrypoint.sh"]
