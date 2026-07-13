ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:9.2.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install Prometheus (single static binary + promtool)
ARG BUILD_ARCH=amd64
ARG PROMETHEUS_VERSION="v3.5.0"
RUN \
    ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    \
    && curl -J -L -o /tmp/prometheus.tar.gz \
        "https://github.com/prometheus/prometheus/releases/download/${PROMETHEUS_VERSION}/prometheus-${PROMETHEUS_VERSION#v}.linux-${ARCH}.tar.gz" \
    \
    && mkdir -p /tmp/prometheus \
    && tar -xzf /tmp/prometheus.tar.gz -C /tmp/prometheus --strip-components=1 \
    && mv /tmp/prometheus/prometheus /usr/bin/prometheus \
    && mv /tmp/prometheus/promtool /usr/bin/promtool \
    && chmod +x /usr/bin/prometheus /usr/bin/promtool \
    \
    && rm -fr /tmp/*

# Copy root filesystem
COPY rootfs /

# Ensure service scripts are executable, regardless of how the
# repository was checked out
RUN chmod +x /etc/s6-overlay/s6-rc.d/*/run /etc/s6-overlay/s6-rc.d/*/finish

# Health check
HEALTHCHECK \
    CMD curl --fail http://127.0.0.1:9090/-/healthy || exit 1

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
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/prometheus/DOCS.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
