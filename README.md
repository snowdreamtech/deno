# Deno

![Docker Image Version](https://img.shields.io/docker/v/snowdreamtech/deno)
![Docker Image Size](https://img.shields.io/docker/image-size/snowdreamtech/deno/latest)
![Docker Pulls](https://img.shields.io/docker/pulls/snowdreamtech/deno)
![Docker Stars](https://img.shields.io/docker/stars/snowdreamtech/deno)

Docker Image packaging for Deno, providing a standardized container base with a flexible entrypoint system, multi-architecture support, and consistent configuration patterns across Alpine, Debian, and Rocky Linux distributions.

## Overview

The Docker Deno image serves as a foundational starting point for building Deno-based containerized applications. It provides:

- **Standardized Dockerfiles** with OCI annotations and best practices
- **Flexible Entrypoint System** supporting custom initialization scripts
- **Consistent Environment Configuration** across all variants
- **Multi-Architecture Support** for diverse hardware platforms
- **Three Distribution Variants**: Alpine (lightweight), Debian (default/broad compatibility), Rocky (enterprise)

## Quick Start

```bash
# Pull and run the default Debian variant
docker pull snowdreamtech/deno:debian
docker run -d --name=deno -e TZ=Asia/Shanghai snowdreamtech/deno:debian

# Or using docker-compose
docker-compose up -d
```

## Distribution Variants

### Debian (Default)

The recommended variant for most use cases, providing broad compatibility and rich package availability.

```bash
docker run -d \
  --name=deno \
  -e TZ=Asia/Shanghai \
  --restart unless-stopped \
  snowdreamtech/deno:debian
```

**Supported Architectures**: i386, amd64, arm32v5, arm32v7, arm64, riscv64, ppc64le, s390x

**Base Image**: `snowdreamtech/debian:13.5.0`

### Alpine

A lightweight variant optimized for minimal image size and fast startup times.

```bash
docker run -d \
  --name=deno \
  -e TZ=Asia/Shanghai \
  --restart unless-stopped \
  snowdreamtech/deno:alpine
```

**Supported Architectures**: i386, amd64, arm32v6, arm32v7, arm64, ppc64le, riscv64, s390x

**Base Image**: `snowdreamtech/alpine:3.24.0`

### Rocky

An enterprise-grade variant based on Rocky Linux, suitable for production environments requiring RHEL compatibility.

```bash
docker run -d \
  --name=deno \
  -e TZ=Asia/Shanghai \
  --restart unless-stopped \
  snowdreamtech/deno:rocky
```

**Supported Architectures**: amd64, arm64, ppc64le, s390x

**Base Image**: `snowdreamtech/rocky:10.2.0`

## Build Instructions

### Single Architecture Build

```bash
# Build Debian variant
docker build -t snowdreamtech/deno:debian ./docker/debian/

# Build Alpine variant
docker build -t snowdreamtech/deno:alpine ./docker/alpine/

# Build Rocky variant
docker build -t snowdreamtech/deno:rocky ./docker/rocky/
```

### Multi-Architecture Build

Use `docker buildx` to build images for multiple architectures:

```bash
# Create and use buildx builder
docker buildx create --use --name build --node build --driver-opt network=host

# Build Debian for multiple architectures
docker buildx build \
  --platform=linux/386,linux/amd64,linux/arm/v5,linux/arm/v7,linux/arm64,linux/riscv64,linux/ppc64le,linux/s390x \
  -t snowdreamtech/deno:debian \
  ./docker/debian/ \
  --push

# Build Alpine for multiple architectures
docker buildx build \
  --platform=linux/386,linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64,linux/ppc64le,linux/riscv64,linux/s390x \
  -t snowdreamtech/deno:alpine \
  ./docker/alpine/ \
  --push

# Build Rocky for multiple architectures
docker buildx build \
  --platform=linux/amd64,linux/arm64,linux/ppc64le,linux/s390x \
  -t snowdreamtech/deno:rocky \
  ./docker/rocky/ \
  --push
```

## Environment Variables

All variants support the following environment variables for runtime configuration:

| Variable | Default | Description |
|----------|---------|-------------|
| `KEEPALIVE` | `0` | Keep container running (1=enable, 0=disable) |
| `CAP_NET_BIND_SERVICE` | `0` | Enable binding to privileged ports (<1024) |
| `LANG` | `C.UTF-8` | Locale for UTF-8 character support |
| `UMASK` | `022` | Default file creation mask |
| `DEBUG` | `false` | Enable debug output in entrypoint scripts |
| `PGID` | `0` | Custom primary group ID for user creation |
| `PUID` | `0` | Custom user ID for user creation |
| `USER` | `root` | Custom username for user creation |
| `WORKDIR` | `/root` | Working directory path |
| `TZ` | - | Timezone (e.g., `Asia/Shanghai`, `America/New_York`) |
| `DENO_VERSION` | - | Deno Version |

**Debian Specific**:

| Variable | Default | Description |
|----------|---------|-------------|
| `DEBIAN_FRONTEND` | `noninteractive` | Debian package installation mode |

### Custom User Creation

In this Deno image, the underlying base image automatically handles user creation. You only need to pass the parameters at runtime:

```bash
docker run -d \
  --name=deno \
  -e PUID=1000 \
  -e PGID=1000 \
  -e USER=appuser \
  snowdreamtech/deno:debian
```

**Note**: The entrypoint scripts only modify permissions and create the user if `PUID≠0`, `PGID≠0`, and `USER≠root`.

## Docker Compose Examples

### Simple Configuration

```yaml
services:
  deno:
    image: snowdreamtech/deno:debian
    container_name: deno
    environment:
      - TZ=Asia/Shanghai
    restart: unless-stopped
```

### Advanced Configuration

```yaml
services:
  deno:
    image: snowdreamtech/deno:debian
    container_name: deno
    environment:
      - TZ=Asia/Shanghai
      - DEBUG=true
      - KEEPALIVE=1
    volumes:
      - /path/to/data:/data
    restart: unless-stopped
```

## Semantic Version Tags

Images follow semantic versioning with the format: `{major}.{minor}.{patch}-{variant}`

Examples:

- `snowdreamtech/deno:2.8.3-debian`
- `snowdreamtech/deno:2.7.4-alpine`
- `snowdreamtech/deno:2.8.3-rocky`

This format allows for:

- **Exact version pinning**: `2.8.3-debian` (precise version)
- **Variant latest tag**: `latest-debian` (tracks latest Debian version)
- **Global latest tag**: `latest` (tracks latest version, defaults to Debian)

## Architecture Support

Each distribution variant supports multiple CPU architectures, deployable across diverse hardware platforms:

| Variant | Architectures |
|---------|---------------|
| **Debian** | i386, amd64, arm32v5, arm32v7, arm64, riscv64, ppc64le, s390x |
| **Alpine** | i386, amd64, arm32v6, arm32v7, arm64, ppc64le, riscv64, s390x |
| **Rocky** | amd64, arm64, ppc64le, s390x |

Docker automatically selects the appropriate architecture for your platform when pulling the image.

## Entrypoint System

The base template includes a flexible entrypoint system that executes custom initialization scripts before starting your application.

### How it Works

1. The `docker-entrypoint.sh` script runs on container startup
2. It executes all executable scripts in `/usr/local/bin/entrypoint.d/` in lexical order
3. Each script receives the container's command-line arguments
4. If any script fails, the container stops (fail-fast behavior)

### Adding Custom Initialization

Create custom initialization scripts in your derived Dockerfiles:

```dockerfile
FROM snowdreamtech/deno:debian

# Add your custom initialization script
COPY my-init.sh /usr/local/bin/entrypoint.d/20-my-init.sh
RUN chmod +x /usr/local/bin/entrypoint.d/20-my-init.sh

# Your application setup
COPY app /app
CMD ["deno", "run", "--allow-net", "/app/main.ts"]
```

### Debug Mode

Enable debug output to troubleshoot entrypoint execution:

```bash
docker run -e DEBUG=true snowdreamtech/deno:debian
```

Example output:

```
→ [ENTRYPOINT] Executing all scripts in /usr/local/bin/entrypoint.d
→ Running /usr/local/bin/entrypoint.d/10-base-init.sh
→ [ENTRYPOINT] Done.
```

## Reference

1. [使用 buildx 构建多平台 Docker 镜像](https://icloudnative.io/posts/multiarch-docker-with-buildx/)
2. [如何使用 docker buildx 构建跨平台 Go 镜像](https://waynerv.com/posts/building-multi-architecture-images-with-docker-buildx/#buildx-%E7%9A%84%E8%B7%A8%E5%B9%B3%E5%8F%B0%E6%9E%84%E5%BB%BA%E7%AD%96%E7%95%A5)
3. [Building Multi-Arch Images for Arm and x86 with Docker Desktop](https://www.docker.com/blog/multi-arch-images/)
4. [How to Rapidly Build Multi-Architecture Images with Buildx](https://www.docker.com/blog/how-to-rapidly-build-multi-architecture-images-with-buildx/)
5. [Faster Multi-Platform Builds: Dockerfile Cross-Compilation Guide](https://www.docker.com/blog/faster-multi-platform-builds-dockerfile-cross-compilation-guide/)
6. [docker/buildx](https://github.com/docker/buildx)

## Contact (Note: deno)

* Email: sn0wdr1am@qq.com
* QQ: 3217680847
* QQ Group: 949022145
* WeChat/WeChat Group: sn0wdr1am

## License

MIT
