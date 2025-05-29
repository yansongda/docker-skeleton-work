FROM yansongda/php:cli-8.3-alpine

LABEL maintainer="yansongda <me@yansongda.cn>"

COPY extensions /tmp/extensions

RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
    && apk add -U --no-cache --virtual .build-deps $DEPENDENCIES \
    && apk add -U --no-cache imap-dev \
    && docker-php-ext-configure imap --with-imap --with-imap-ssl \
    && docker-php-ext-install imap \
    && docker-php-ext-enable imap \
    && php -m \
    # Clean
    && apk del .build-deps \
    && rm -rf /tmp/* ~/.pearrc /var/cache/apk/* \
    && cd && rm -rf $BUILD_DIR

EXPOSE 8080 8888 8889

ENTRYPOINT ["php", "/www/bin/hyperf.php", "start"]