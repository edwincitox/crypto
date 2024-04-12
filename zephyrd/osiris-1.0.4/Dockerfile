# Builder stage
FROM debian:stable-slim AS builder
RUN apt-get update && apt-get full-upgrade -y && apt-get install --no-install-recommends -y ca-certificates wget unzip && apt-get clean
WORKDIR /app
RUN wget -O zephyr.zip https://github.com/ZephyrProtocol/zephyr/releases/download/v1.0.4/zephyr-cli-linux-v1.0.4.zip && \
    unzip zephyr.zip && \
    rm zephyr.zip && \
    mv zephyr* zephyr && \
    chmod -Rf +x zephyr/*

# Runtime stage
FROM debian:stable-slim

COPY --from=builder /app/zephyr/* /usr/bin/

# Create zephyr user
RUN addgroup --gid 1000 zephyr && \
    adduser --system --disabled-password --uid 1000 --ingroup zephyr zephyr && \
    mkdir -p /home/zephyr/.bitzephyr && \
    chown -Rf zephyr:zephyr /home/zephyr

# Contains the blockchain
VOLUME /home/zephyr/.bitzephyr

EXPOSE 18080
EXPOSE 18081
EXPOSE 18082

# switch to user zephyr
USER zephyr

# Healthcheck
HEALTHCHECK --interval=1m --timeout=30s CMD zephyrd status || exit 1

ENTRYPOINT ["zephyrd"]
CMD ["--data-dir=/home/zephyr/.bitzephyr", "--non-interactive", "--prune-blockchain", "--no-igd", "--p2p-bind-ip=0.0.0.0", "--p2p-bind-port=18080", "--rpc-bind-ip=0.0.0.0", "--rpc-bind-port=18081", "--zmq-rpc-bind-ip=0.0.0.0", "--zmq-rpc-bind-por=18082", "--confirm-external-bind", "--disable-dns-checkpoints", "--enable-dns-blocklist"]