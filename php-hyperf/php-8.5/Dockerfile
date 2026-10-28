FROM yansongda/php:cli-8.5-alpine

LABEL maintainer="yansongda <me@yansongda.cn>"

# 注：PHP >= 8.4 已将 imap 扩展移出核心，故此处不再安装 imap
RUN set -ex \
    && uname -a \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/php.ini \
    && sed -i 's/memory_limit = 512M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini \
    && printf '\n[openssl_init]\nssl_conf = ssl_sect\n\n[ssl_sect]\nsystem_default = system_default_sect\n\n[system_default_sect]\nOptions = UnsafeLegacyRenegotiation\n' >> /etc/ssl/openssl.cnf

EXPOSE 8080 8888 8889

ENTRYPOINT ["php", "/www/bin/hyperf.php", "start"]
