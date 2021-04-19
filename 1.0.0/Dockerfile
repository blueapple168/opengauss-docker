FROM centos:7.8.2003

# Need to choose to download files based on platform architecture
# Ver  OS    SHA256
# 1.0.0   centos_x86_64   c8fc757de0438a3e06add570b54b34561147a7aeb847b7eb72f2d161550670eb
# https://opengauss.obs.cn-south-1.myhuaweicloud.com/1.0.0/x86/openGauss-1.0.0-CentOS-64bit.tar.gz   
# 1.0.0   openeuler_aarch64   5e7f4030f2d5a10794672d49aaf7012e0f5cea2f061d9ee69b49b2f15e522b1f
# https://opengauss.obs.cn-south-1.myhuaweicloud.com/1.0.0/arm/openGauss-1.0.0-openEuler-64bit.tar.gz
ADD https://opengauss.obs.cn-south-1.myhuaweicloud.com/1.0.0/x86/openGauss-1.0.0-CentOS-64bit.tar.gz /tmp/

ENV LANG en_US.utf8

RUN set -eux; \
    groupadd -g 70 gaussdb; \
    useradd -u 70 -g gaussdb -d /home/gaussdb gaussdb; \
    yum install -y bzip2 bzip2-devel curl libaio && \
    mkdir -p /var/lib/opengauss && \
    mkdir -p /usr/local/opengauss && \
    tar -zxvf /tmp/openGauss-1.0.0-CentOS-64bit.tar.gz -C /usr/local/opengauss && \
    mkdir -p /var/run/opengauss && \
    chown -R gaussdb:gaussdb /var/run/opengauss && \
    chmod 2777 /var/run/opengauss && \
    rm -rf /tmp/openGauss-1.0.0-CentOS-64bit.tar.gz && \
    # Set Timezone CST
    rm -rf /etc/localtime && \
    ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    # Clean
    yum clean all

RUN set -eux; \
    echo "export GAUSSHOME=/usr/local/opengauss"  >> /home/gaussdb/.bashrc && \
    echo "export PATH=\$GAUSSHOME/bin:\$PATH " >> /home/gaussdb/.bashrc && \
    echo "export LD_LIBRARY_PATH=\$GAUSSHOME/lib:\$LD_LIBRARY_PATH" >> /home/gaussdb/.bashrc

ENV GOSU_VERSION 1.12
RUN set -eux; \
    gpg --keyserver pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-amd64" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-amd64.asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && rm -r /root/.gnupg/ \
    && chmod +x /usr/local/bin/gosu

RUN mkdir /docker-entrypoint-initdb.d

ENV PGDATA /var/lib/opengauss/data

COPY docker-entrypoint.sh /usr/local/bin/
RUN chmod 755 /usr/local/bin/entrypoint.sh; ln -s /usr/local/bin/docker-entrypoint.sh / # backwards compat

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 5432
CMD ["gaussdb"]
