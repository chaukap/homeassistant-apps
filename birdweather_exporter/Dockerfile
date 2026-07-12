ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:9.2.0

# Take the exporter binary from the multi-arch release image published by
# https://github.com/chaukap/birdweather-prometheus-exporter — the local
# build pulls the slice matching the machine's architecture.
FROM ghcr.io/chaukap/birdweather-prometheus-exporter:v0.1.0@sha256:f2fa1e1bace126761f1884f3dc51f064497e8f79f96b7ec32e9cf4aba47eafb5 AS exporter

# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

COPY --from=exporter /birdweather-exporter /usr/bin/birdweather-exporter

# Copy root filesystem
COPY rootfs /

# Ensure service scripts are executable, regardless of how the
# repository was checked out
RUN chmod +x /etc/s6-overlay/s6-rc.d/*/run /etc/s6-overlay/s6-rc.d/*/finish

# Health check
HEALTHCHECK \
    CMD curl --fail http://127.0.0.1:9743/healthz || exit 1

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Chandler Haukap <chandlerhaukap@gmail.com>" \
    org.opencontainers.image.vendor="Chandler Haukap's Home Assistant Apps" \
    org.opencontainers.image.authors="Chandler Haukap <chandlerhaukap@gmail.com>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/birdweather_exporter/DOCS.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
