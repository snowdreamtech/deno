FROM snowdreamtech/debian:12.10.0

# OCI annotations to image
LABEL org.opencontainers.image.authors="Snowdream Tech" \
    org.opencontainers.image.title="Deno Image Based On Debian" \
    org.opencontainers.image.description="Docker Images for Deno on Debian. (i386,amd64,arm32v5,arm32v7,arm64,mips64le,ppc64le,s390x)" \
    org.opencontainers.image.documentation="https://hub.docker.com/r/snowdreamtech/deno" \
    org.opencontainers.image.base.name="snowdreamtech/deno:debian" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/snowdreamtech/deno" \
    org.opencontainers.image.vendor="Snowdream Tech" \
    org.opencontainers.image.version="2.1.9" \
    org.opencontainers.image.url="https://github.com/snowdreamtech/deno"

# Switch to the user
USER root

# Set the workdir
WORKDIR /root

# keep the docker container running
ENV KEEPALIVE=0 \
    # The cap_net_bind_service capability in Linux allows a process to bind a socket to Internet domain privileged ports, 
    # which are port numbers less than 1024. 
    CAP_NET_BIND_SERVICE=0

ENV DENO_VERSION=2.1.9

ARG GID=1000 \
    UID=1000  \
    USER=deno \
    WORKDIR=/home/deno

# Create a user with UID and GID
RUN set -eux \
    && if [ "${USER}" != "root" ]; then \
    addgroup --gid ${GID} ${USER}; \
    adduser --home /home/${USER} --uid ${UID} --gid ${GID} --gecos ${USER} --shell /bin/bash --disabled-password ${USER}; \
    # sed -i "/%sudo/c ${USER} ALL=(ALL:ALL) NOPASSWD:ALL" /etc/sudoers; \
    fi \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy --purge autoremove \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/* 

# Enable CAP_NET_BIND_SERVICE
RUN set -eux \
    && if [ "${USER}" != "root" ] && [ "${CAP_NET_BIND_SERVICE}" -eq 1 ]; then \
    DEBIAN_FRONTEND=noninteractive apt-get -qqy update; \
    DEBIAN_FRONTEND=noninteractive apt-get -qqy install --no-install-recommends libcap2-bin; \
    # setcap 'cap_net_bind_service=+ep' `which nginx`; \
    fi \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy --purge autoremove \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/* 

RUN set -eux \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy update  \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy install --no-install-recommends \ 
    unzip \
    npm \
    yarnpkg \
    && curl -fsSL https://deno.land/install.sh | DENO_INSTALL=/usr sh -s -- --yes \
    && ln -s /usr/bin/yarnpkg /usr/bin/yarn \
    && npm install -g pnpm \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy --purge autoremove \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/*

# Switch to the user
USER ${USER}

# Set the workdir
WORKDIR ${WORKDIR}

COPY docker-entrypoint.sh /usr/local/bin/

ENTRYPOINT ["docker-entrypoint.sh"]