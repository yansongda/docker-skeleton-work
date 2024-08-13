FROM yansongda/php:cli-7.4-bullseye

LABEL maintainer="yansongda <me@yansongda.cn>"

COPY extensions /tmp/extensions

RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
    && chmod 777 /tmp && apt-get update && apt-get -y install vim unzip \
    # 解压 zip 扩展
    && cd /tmp/extensions/ && unzip dm.zip && unzip kdb.zip \
    # amd64 才安装 kafka sqlsrv 等扩展， arm64 的不处理
    && if [ $(uname -a | grep -c "x86_64") -gt 0 ]; then \
        chmod 1777 /tmp; \
        apt-get update; \
        apt-get install -y $DEPENDENCIES; \
        apt-get install -y librdkafka-dev libxml2-dev unixodbc-dev sox; \
        pecl install -o -f rdkafka-5.0.2 sqlsrv-5.10.1 pdo_sqlsrv-5.10.1; \
        docker-php-ext-install soap; \
        docker-php-ext-enable rdkafka sqlsrv pdo_sqlsrv; \
        # for sqlsvr
        curl https://packages.microsoft.com/keys/microsoft.asc | tee /etc/apt/trusted.gpg.d/microsoft.asc; \
        curl https://packages.microsoft.com/config/debian/11/prod.list | tee /etc/apt/sources.list.d/mssql-release.list; \
        apt-get update; \
        ACCEPT_EULA=Y apt-get install -y msodbcsql17; \
        # for kdb
        cp /tmp/extensions/kdb/x86/* /lib/; \
        cp /tmp/extensions/kdb/x86/* /usr/local/lib/php/extensions/no-debug-non-zts-20190902/; \
        echo "extension=pdo_kdb" > /usr/local/etc/php/conf.d/pdo_kdb.ini; \
        # for dm
        mkdir -p /etc/ld.so.conf.d && cp /tmp/extensions/dm/x86/* /etc/ld.so.conf.d/ \
        echo "/usr/lib64/libdem" > /etc/ld.so.conf.d/libdem.conf && rm -rf /etc/ld.so.cache && echo "CHAR_CODE=(PG_UTF8)" > /etc/dm_svc.conf \
        cp /tmp/extensions/dm/x86/php74_pdo_dm.so /usr/local/lib/php/extensions/no-debug-non-zts-20190902/; \
        echo "extension=php74_pdo_dm" > /usr/local/etc/php/conf.d/pdo_dm.ini; \
        # For me
        php --ri rdkafka && php --ri sqlsrv && php --ri pdo_sqlsrv && php --ri pdo_kdb && php --ri php74_pdo_dm && sox --version; \
    else \
        # for kdb
        cp /tmp/extensions/kdb/arm64/* /lib/; \
        cp /tmp/extensions/kdb/arm64/* /usr/local/lib/php/extensions/no-debug-non-zts-20190902/; \
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
