FROM php:7.2-fpm

# Set environmental variables
ENV LOCAL_UID 33
ENV COMPOSER_HOME /root/composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

# Install dependencies
RUN set -xe \
	# Install deps
	&& apt-get update && apt-get install -y --force-yes \
		libicu-dev \
		zlib1g-dev \
		libmcrypt-dev \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libmcrypt-dev \
		libpng-dev \
		libxml2-dev \
		libicu57 \
		zlib1g \
		sudo \
		libzip-dev \
	--no-install-recommends && rm -r /var/lib/apt/lists/*

# Install extensions
RUN set -xe \
	&& docker-php-ext-configure \
		gd \
		    --with-gd \
			--with-freetype-dir=/usr/include/ \
			--with-png-dir=/usr/include/ \
			--with-jpeg-dir=/usr/include/ \
		\
	\
	&& docker-php-ext-configure zip --with-libzip; \
	&& docker-php-ext-install -j$(nproc) \
		gd \
		intl \
		mbstring \
		mcrypt \
		pdo_mysql \
		zip

RUN pecl install \
		apcu-5.1.12 \
	; \
    pecl clear-cache; \
    docker-php-ext-enable \
    	apcu \
    	opcache

# PHP settings
RUN { \
		echo "memory_limit=1024M"; \
		echo "date.timezone=Europe/Berlin"; \
		echo "detect_unicode=off"; \
		echo "xdebug.scream=off"; \
	} > $PHP_INI_DIR/conf.d/custom.ini

# Install composer
RUN set -xe \
	&& apt-get update && apt-get install -y git libfontconfig1 --no-install-recommends && rm -r /var/lib/apt/lists/* \
	&& curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install prestissimo for faster composer installations
RUN composer global require "hirak/prestissimo:^0.3"

# Install phpunit
RUN composer global require phpunit/phpunit 6.0.*

COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

WORKDIR /var/www
EXPOSE 9000
CMD ["php-fpm"]
