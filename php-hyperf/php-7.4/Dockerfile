FROM yansongda/php:cli-7.4-bullseye

LABEL maintainer="yansongda <me@yansongda.cn>"

COPY extensions /tmp/extensions

RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
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
        cp /tmp/extensions/kdb/x86/* /usr/local/lib/php/extensions/no-debug-non-zts-20190902/; \
        echo "extension=pdo_kdb" > /usr/local/etc/php/conf.d/pdo_kdb.ini; \
        # For me
        php --ri rdkafka && php --ri sqlsrv && php --ri pdo_sqlsrv && php --ri pdo_kdb && sox --version; \
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