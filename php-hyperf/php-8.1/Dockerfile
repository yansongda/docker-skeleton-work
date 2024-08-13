FROM yansongda/php:cli-8.1-bookworm

LABEL maintainer="yansongda <me@yansongda.cn>"

COPY extensions /tmp/extensions

RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
    && chmod 777 /tmp && apt-get update && apt-get -y install vim unzip \
    # 解压 zip 扩展
    && cd /tmp/extensions/ && unzip dm.zip && unzip kdb.zip \
    && if [ $(uname -a | grep -c "x86_64") -gt 0 ]; then \
        # for kdb
        cp /tmp/extensions/kdb/x86/* /lib/; \
        cp /tmp/extensions/kdb/x86/* /usr/local/lib/php/extensions/no-debug-non-zts-20210902/; \
        echo "extension=pdo_kdb" > /usr/local/etc/php/conf.d/pdo_kdb.ini; \
        # for dm
        mkdir -p /etc/ld.so.conf.d && cp /tmp/extensions/dm/x86/* /etc/ld.so.conf.d/; \
        echo "/usr/lib64/libdem" > /etc/ld.so.conf.d/libdem.conf; \
        rm -rf /etc/ld.so.cache; \
        echo "CHAR_CODE=(PG_UTF8)" > /etc/dm_svc.conf; \
        cp /tmp/extensions/dm/x86/php81_pdo_dm.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902/; \
        echo "extension=php81_pdo_dm" > /usr/local/etc/php/conf.d/pdo_dm.ini; \
        # For me
        php --ri pdo_kdb && php --ri php81_pdo_dm; \
    else \
        # for kdb
        cp /tmp/extensions/kdb/arm64/* /lib/; \
        cp /tmp/extensions/kdb/arm64/* /usr/local/lib/php/extensions/no-debug-non-zts-20210902/; \
        echo "extension=pdo_kdb" > /usr/local/etc/php/conf.d/pdo_kdb.ini; \
        # For me
        php --ri pdo_kdb; \
    fi \
    && php -m \
    # Clean
    && apt-get -y remove $DEPENDENCIES \
    && apt-get purge -y --auto-remove \
    && rm -rf /var/lib/apt/lists/* /tmp ~/.pearrc

EXPOSE 8080 8888 8889

ENTRYPOINT ["php", "/www/bin/hyperf.php", "start"]