FROM snowdreamtech/alpine:3.22.2

# OCI annotations to image
LABEL org.opencontainers.image.authors="Snowdream Tech" \
    org.opencontainers.image.title="Deno Image Based On Alpine" \
    org.opencontainers.image.description="Docker Images for Deno on Alpine. (amd64, arm64)" \
    org.opencontainers.image.documentation="https://hub.docker.com/r/snowdreamtech/deno" \
    org.opencontainers.image.base.name="snowdreamtech/deno:alpine" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/snowdreamtech/deno" \
    org.opencontainers.image.vendor="Snowdream Tech" \
    org.opencontainers.image.version="2.3.1" \
    org.opencontainers.image.url="https://github.com/snowdreamtech/deno"
    
# Switch to the user
USER root

# Set the workdir
WORKDIR /root

# keep the docker container running
ARG KEEPALIVE=0 \
    # The cap_net_bind_service capability in Linux allows a process to bind a socket to Internet domain privileged ports, 
    # which are port numbers less than 1024. 
    CAP_NET_BIND_SERVICE=0 \
    # Ensure the container exec commands handle range of utf8 characters based of
    # default locales in base image (https://github.com/docker-library/docs/tree/master/debian#locales)
    LANG=C.UTF-8\
    UMASK=022 \
    DEBUG=false \
    PGID=0 \
    PUID=0  \
    USER=root \
    WORKDIR=/root 

# keep the docker container running
ENV KEEPALIVE=${KEEPALIVE} \
    # The cap_net_bind_service capability in Linux allows a process to bind a socket to Internet domain privileged ports, 
    # which are port numbers less than 1024. 
    CAP_NET_BIND_SERVICE=${CAP_NET_BIND_SERVICE} \
    # Ensure the container exec commands handle range of utf8 characters based of
    # default locales in base image (https://github.com/docker-library/docs/tree/master/debian#locales)
    LANG=${LANG} \
    UMASK=${UMASK} \
    DEBUG=${DEBUG} \
    PGID=${PGID} \
    PUID=${PUID}  \
    USER=${USER} \
    WORKDIR=${WORKDIR} 

ENV DENO_VERSION=2.3.1-r2

RUN apk add --no-cache \
    unzip \
    deno=${DENO_VERSION}

# Create a user with PUID and PGID
RUN if [ "${USER}" != "root" ]; then \
    addgroup -g ${PGID} ${USER}; \
    adduser -h /home/${USER} -u ${PUID} -g ${USER} -G ${USER} -s /bin/sh -D ${USER}; \
    # sed -i "/%sudo/c ${USER} ALL=(ALL:ALL) NOPASSWD:ALL" /etc/sudoers; \
    fi

# Enable CAP_NET_BIND_SERVICE
# RUN if [ "${USER}" != "root" ] && [ "${CAP_NET_BIND_SERVICE}" -eq 1 ]; then \
    # setcap 'cap_net_bind_service=+ep' `which nginx`; \
    # fi

COPY entrypoint.d /usr/local/bin/entrypoint.d

COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh

RUN chmod +x /usr/local/bin/docker-entrypoint.sh \
    && chmod +x /usr/local/bin/entrypoint.d/*

ENTRYPOINT ["docker-entrypoint.sh"]