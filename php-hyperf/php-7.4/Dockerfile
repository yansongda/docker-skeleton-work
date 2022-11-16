FROM registry.cn-shenzhen.aliyuncs.com/yansongda/skeleton:hyperf-7.4

LABEL maintainer="yansongda <me@yansongda.cn>"

# amd64 才安装 kafka sqlsrv 等扩展， arm64 的不处理
RUN uname -a \
    && if (( $(uname -a | grep -c x86_64) >= 1 )); then \
        sed -i 's/mirrors.tuna.tsinghua.edu.cn/dl-cdn.alpinelinux.org/g' /etc/apk/repositories; \
        apk add -U --no-cache --virtual .build-deps $DEPENDENCIES; \
        apk add -U --no-cache librdkafka-dev libxml2-dev unixodbc-dev; \
        pecl install -o -f rdkafka-5.0.2 sqlsrv-5.10.1 pdo_sqlsrv-5.10.1; \
        docker-php-ext-install soap; \
        docker-php-ext-enable rdkafka sqlsrv pdo_sqlsrv; \
        php -m && uname -a; \
        # for sqlsvr
        curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.9.1.1-1_amd64.apk; \
        apk add --allow-untrusted msodbcsql17_17.9.1.1-1_amd64.apk; \
        # Clean
        apk del .build-deps; \
        rm -rf /tmp/* ~/.pearrc /var/cache/apk/* msodbcsql17_17.9.1.1-1_amd64.apk; \
        cd && rm -rf $BUILD_DIR; \
        # For me
        sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories; \
        php --ri rdkafka && php --ri sqlsrv && php --ri pdo_sqlsrv; \
    fi