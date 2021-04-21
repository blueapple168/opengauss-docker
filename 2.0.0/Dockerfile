FROM ubuntu:18.04

# Need to choose to download files based on platform architecture
# Ver  OS    SHA256
# 2.0.0   centos_x86_64
# https://github.com/opengauss-mirror/openGauss-server/archive/refs/tags/v2.0.0.tar.gz
# ADD https://github.com/opengauss-mirror/openGauss-server/archive/refs/tags/v2.0.0.tar.gz /tmp/
COPY wal2json.so /tmp
ENV LANG en_US.utf8

RUN set -eux; \
    groupadd -g 70 gaussdb; \
    useradd -u 70 -g gaussdb -d /home/gaussdb gaussdb; \
    apt-get update && apt-get install -y \
    curl \
    libaio-dev \
    libkeyutils-dev \
    locales \
    libreadline-dev;\
    rm -rf /var/lib/apt/lists/*; \
    ln -s /lib/x86_64-linux-gnu/libreadline.so.7 /lib/x86_64-linux-gnu/libreadline.so.6; \
    mkdir -p /var/lib/opengauss ; \
    mkdir -p /usr/local/opengauss ; \
    mkdir /docker-entrypoint-initdb.d ; \
    curl -o /tmp/openGauss-server-2.0.0.tar.gz  -SL 'https://github.com/opengauss-mirror/openGauss-server/archive/refs/tags/v2.0.0.tar.gz' ; \
    tar -zxvf /tmp/openGauss-server-2.0.0.tar.gz -C /usr/local/opengauss ; \
    mkdir -p /var/run/opengauss ; \
    chown -R gaussdb:gaussdb /var/run/opengauss /usr/local/opengauss /docker-entrypoint-initdb.d ; \
    cp /tmp/wal2json.so /usr/local/opengauss ; \
    chmod 2777 /var/run/opengauss ; \
    rm -rf /tmp/* ; \
    # Set Timezone CST
    rm -rf /etc/localtime ; \
    ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime ; \
    locale-gen en_US.UTF-8 ; \
    # Set ENV
    echo "export GAUSSHOME=/usr/local/opengauss"  >> /home/gaussdb/.bashrc ; \
    echo "export PATH=\$GAUSSHOME/bin:\$PATH " >> /home/gaussdb/.bashrc ; \
    echo "export LD_LIBRARY_PATH=\$GAUSSHOME/lib:\$LD_LIBRARY_PATH" >> /home/gaussdb/.bashrc

ENV GOSU_VERSION 1.12
RUN set -eux; \
    gpg --keyserver pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 ; \
    curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-amd64" ; \
    curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-amd64.asc" ; \
    gpg --verify /usr/local/bin/gosu.asc ; \
    rm /usr/local/bin/gosu.asc ; \
    rm -r /root/.gnupg/ ; \
    chmod +x /usr/local/bin/gosu

ENV PGDATA /var/lib/opengauss/data
COPY docker-entrypoint.sh /usr/local/bin/
RUN chmod 755 /usr/local/bin/entrypoint.sh ; \
    # backwards compat
    ln -s /usr/local/bin/docker-entrypoint.sh /

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 5432
USER gaussdb
CMD ["gaussdb"]
