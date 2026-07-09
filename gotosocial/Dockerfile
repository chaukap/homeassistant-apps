ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:9.2.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install GoToSocial (single static binary + bundled web assets)
ARG BUILD_ARCH=amd64
ARG GOTOSOCIAL_VERSION="v0.21.2"
RUN \
    ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    \
    && curl -J -L -o /tmp/gotosocial.tar.gz \
        "https://codeberg.org/superseriousbusiness/gotosocial/releases/download/${GOTOSOCIAL_VERSION}/gotosocial_${GOTOSOCIAL_VERSION#v}_linux_${ARCH}.tar.gz" \
    \
    && mkdir -p /usr/share/gotosocial \
    && tar -xzf /tmp/gotosocial.tar.gz -C /usr/share/gotosocial \
    && mv /usr/share/gotosocial/gotosocial /usr/bin/gotosocial \
    && chmod +x /usr/bin/gotosocial \
    \
    && rm -fr \
        /tmp/* \
        /usr/share/gotosocial/example

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
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/gotosocial/DOCS.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
