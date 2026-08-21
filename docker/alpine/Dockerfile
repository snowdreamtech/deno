# Deno Image (Alpine Variant)
# Extends snowdreamtech/alpine with deno installed.

FROM ghcr.io/snowdreamtech/alpine:3.24.1

# Build-time Configuration
ARG BUILDTIME \
    VERSION \
    REVISION \
    KEEPALIVE=0 \
    CAP_NET_BIND_SERVICE=0 \
    LANG=C.UTF-8 \
    UMASK=022 \
    DEBUG=false \
    PASSWORDLESS_SUDO=false \
    PGID=0 \
    PUID=0 \
    USER=root \
    WORKDIR=/root \
    DENO_VERSION=2.7.4-r2

# Standard OCI Metadata (https://github.com/opencontainers/image-spec)
LABEL org.opencontainers.image.authors="Snowdream Tech" \
    org.opencontainers.image.title="Deno Image Based On Alpine" \
    org.opencontainers.image.description="Docker Images for Deno on Alpine. (amd64, arm64)" \
    org.opencontainers.image.documentation="https://hub.docker.com/r/snowdreamtech/deno" \
    org.opencontainers.image.base.name="ghcr.io/snowdreamtech/alpine:3.24.1" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/snowdreamtech/deno" \
    org.opencontainers.image.vendor="Snowdream Tech" \
    org.opencontainers.image.created="${BUILDTIME}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.url="https://github.com/snowdreamtech/deno"

# x-release-please-start-version
LABEL org.opencontainers.image.version="${VERSION:-2.7.4}"
# x-release-please-end

# Runtime Environment Configuration
ENV KEEPALIVE=${KEEPALIVE} \
    CAP_NET_BIND_SERVICE=${CAP_NET_BIND_SERVICE} \
    LANG=${LANG} \
    UMASK=${UMASK} \
    DEBUG=${DEBUG} \
    PASSWORDLESS_SUDO=${PASSWORDLESS_SUDO} \
    PGID=${PGID} \
    PUID=${PUID} \
    USER=${USER} \
    WORKDIR=${WORKDIR} \
    DENO_VERSION=${DENO_VERSION}


# Additional tooling and configuration for Deno image
RUN apk update \
    && apk add --no-cache \
    unzip \
    deno=${DENO_VERSION}

# Container Orchestration Files
COPY entrypoint.d /usr/local/bin/entrypoint.d

RUN chmod +x /usr/local/bin/entrypoint.d/*

# Standard Healthcheck (Verification of core system responsiveness)
HEALTHCHECK NONE

# Process Execution
USER root
WORKDIR /root
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["/bin/sh"]
