ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:9.2.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install DoneTick (single static binary with embedded frontend)
ARG BUILD_ARCH=amd64
ARG DONETICK_VERSION="v0.1.76"
RUN \
    ARCH="x86_64" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    \
    && curl -J -L -o /tmp/donetick.tar.gz \
        "https://github.com/donetick/donetick/releases/download/${DONETICK_VERSION}/donetick_Linux_${ARCH}.tar.gz" \
    \
    && mkdir -p /tmp/donetick \
    && tar -xzf /tmp/donetick.tar.gz -C /tmp/donetick \
    && mv /tmp/donetick/donetick /usr/bin/donetick \
    && chmod +x /usr/bin/donetick \
    \
    && rm -fr /tmp/*

# Copy root filesystem
COPY rootfs /

# Ensure service scripts are executable, regardless of how the
# repository was checked out
RUN chmod +x /etc/s6-overlay/s6-rc.d/*/run /etc/s6-overlay/s6-rc.d/*/finish

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
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/donetick/DOCS.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
