FROM ghcr.io/home-assistant/base:latest

# Install runtime dependencies and capture tools
RUN apk add --no-cache \
    nodejs \
    npm \
    git \
    curl \
    tcpdump \
    ca-certificates \
    libusb && \
    npm install -g corepack ember-sniff && \
    corepack enable && \
    corepack prepare yarn@4.12.0 --activate

# Build and install whsniff (CC2531 sniffer tool)
RUN apk add --no-cache --virtual .whsniff-build-deps \
    build-base \
    libusb-dev && \
    cd /tmp && \
    curl -fsSL https://github.com/homewsn/whsniff/archive/v1.3.tar.gz | tar zx && \
    cd whsniff-1.3 && \
    make && \
    make install && \
    apk del .whsniff-build-deps && \
    rm -rf /tmp/whsniff-1.3

# Install ViLocal from GitHub
RUN cd /tmp && \
    git clone https://github.com/kristian/ViLocal.git && \
    cd ViLocal && \
    corepack yarn install --immutable && \
    mv /tmp/ViLocal /app

# Create app structure
WORKDIR /app

# Copy root filesystem
COPY rootfs /

# Create data directory for persistent storage
RUN mkdir -p /data

# Labels
LABEL \
    org.opencontainers.image.title="Home Assistant App: ViLocal" \
    org.opencontainers.image.description="Local ZigBee sniffing integration for Viessmann ViCare devices" \
    org.opencontainers.image.source="https://github.com/kristian/ViLocal-ha_app" \
    org.opencontainers.image.licenses="Apache License 2.0"

RUN chmod a+x /run.sh /app/start.sh
CMD [ "/run.sh" ]
