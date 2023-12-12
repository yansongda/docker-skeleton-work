FROM yansongda/php:cli-8.1-bookworm

LABEL maintainer="yansongda <me@yansongda.cn>"

COPY extensions /tmp/extensions

RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
    # for kdb
    && cp /tmp/extensions/kdb/arm64/* /lib/ \
    && cp /tmp/extensions/kdb/arm64/* /usr/local/lib/php/extensions/no-debug-non-zts-20220829/ \
    && echo "extension=pdo_kdb" > /usr/local/etc/php/conf.d/pdo_kdb.ini \
    # For me
    && php --ri pdo_kdb \
    && php -m \
    # Clean
    && apt-get -y remove $DEPENDENCIES \
    && apt-get purge -y --auto-remove \
    && rm -rf /var/lib/apt/lists/* /tmp ~/.pearrc