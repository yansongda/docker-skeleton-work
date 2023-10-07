FROM yansongda/skeleton:hyperf-7.4

LABEL maintainer="yansongda <me@yansongda.cn>"

# amd64 才安装 kafka sqlsrv 等扩展， arm64 的不处理
RUN uname -a \
    && if [ $(uname -a | grep -c "x86_64") -gt 0 ]; then \
        apt-get update \
        apt-get install -y $DEPENDENCIES \
        apt-get install -y librdkafka-dev libxml2-dev unixodbc-dev sox \
        pecl install -o -f rdkafka-5.0.2 sqlsrv-5.10.1 pdo_sqlsrv-5.10.1 \
        docker-php-ext-install soap \
        docker-php-ext-enable rdkafka sqlsrv pdo_sqlsrv \
        php -m && uname -a \
        # for sqlsvr
        curl https://packages.microsoft.com/keys/microsoft.asc | sudo tee /etc/apt/trusted.gpg.d/microsoft.asc \
        curl https://packages.microsoft.com/config/debian/11/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list \
        apt-get update \
        ACCEPT_EULA=Y apt-get install -y msodbcsql17 \
        # Clean
        apt-get -y remove $DEPENDENCIES \
        apt-get purge -y --auto-remove \
        rm -rf /var/lib/apt/lists/* /tmp/pear ~/.pearrc \
        # For me
        php --ri rdkafka && php --ri sqlsrv && php --ri pdo_sqlsrv && sox --version; \
    fi