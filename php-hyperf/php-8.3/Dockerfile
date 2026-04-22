FROM yansongda/php:cli-8.3-alpine

LABEL maintainer="yansongda <me@yansongda.cn>"

RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
    && printf '\n[openssl_init]\nssl_conf = ssl_sect\n\n[ssl_sect]\nsystem_default = system_default_sect\n\n[system_default_sect]\nOptions = UnsafeLegacyRenegotiation\n' >> /etc/ssl/openssl.cnf \
    # 临时切换到 Alpine 官方源，避免 USTC 镜像 DNS 解析失败
    && sed -i 's/mirrors.ustc.edu.cn\/alpine/dl-cdn.alpinelinux.org\/alpine/g' /etc/apk/repositories \
    && apk add -U --no-cache --virtual .build-deps $DEPENDENCIES \
    && apk add -U --no-cache imap-dev \
    && docker-php-ext-configure imap --with-imap --with-imap-ssl \
    && docker-php-ext-install imap \
    && docker-php-ext-enable imap \
    && php -m \
    # Clean
    && apk del .build-deps \
    && rm -rf /tmp/* ~/.pearrc /var/cache/apk/* \
    && cd && rm -rf $BUILD_DIR \
    # 构建完成后切回 USTC 镜像源
    && sed -i 's/dl-cdn.alpinelinux.org\/alpine/mirrors.ustc.edu.cn\/alpine/g' /etc/apk/repositories

EXPOSE 8080 8888 8889

ENTRYPOINT ["php", "/www/bin/hyperf.php", "start"]
