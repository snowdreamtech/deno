FROM snowdreamtech/alpine:3.21.3

# OCI annotations to image
LABEL org.opencontainers.image.authors="Snowdream Tech" \
    org.opencontainers.image.title="Deno Image Based On Alpine" \
    org.opencontainers.image.description="Docker Images for Deno on Alpine. (amd64, arm64)" \
    org.opencontainers.image.documentation="https://hub.docker.com/r/snowdreamtech/deno" \
    org.opencontainers.image.base.name="snowdreamtech/deno:alpine" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/snowdreamtech/deno" \
    org.opencontainers.image.vendor="Snowdream Tech" \
    org.opencontainers.image.version="2.0.6" \
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

ENV DENO_VERSION=2.0.6-r0

ARG GID=1000 \
    UID=1000  \
    USER=deno \
    WORKDIR=/home/deno

# Create a user with UID and GID
RUN if [ "${USER}" != "root" ]; then \
    addgroup -g ${GID} ${USER}; \
    adduser -h /home/${USER} -u ${UID} -g ${USER} -G ${USER} -s /bin/sh -D ${USER}; \
    # sed -i "/%sudo/c ${USER} ALL=(ALL:ALL) NOPASSWD:ALL" /etc/sudoers; \
    fi

# Enable CAP_NET_BIND_SERVICE
RUN if [ "${USER}" != "root" ] && [ "${CAP_NET_BIND_SERVICE}" -eq 1 ]; then \
    apk add --no-cache libcap; \
    # setcap 'cap_net_bind_service=+ep' `which nginx`; \
    fi

RUN apk add --no-cache \
    unzip \
    deno=${DENO_VERSION} \
    npm \
    pnpm@community \
    yarn
    
# Switch to the user
USER ${USER}

# Set the workdir
WORKDIR ${WORKDIR}

COPY docker-entrypoint.sh /usr/local/bin/

ENTRYPOINT ["docker-entrypoint.sh"]