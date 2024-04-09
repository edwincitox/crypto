# Builder stage
FROM debian:stable-slim AS builder
RUN apt-get update && apt-get full-upgrade -y && apt-get install --no-install-recommends -y ca-certificates wget bzip2 && apt-get clean
WORKDIR /app
RUN case $(uname -m) in \
    x86_64) url=https://downloads.getmonero.org/cli/linux64 ;; \
    i686) url=https://downloads.getmonero.org/cli/linux32 ;; \
    aarch64) url=https://downloads.getmonero.org/cli/linuxarm8 ;; \
    arm*) url=https://downloads.getmonero.org/cli/linuxarm7 ;; \
    *) echo "unsupported architecture"; exit 1 ;; \
    esac && \
    wget -O monero.tar.bz2 $url && \
    tar -xjf monero.tar.bz2 && \
    rm monero.tar.bz2 && \
    mv monero* monero && \
    rm monero/*.md monero/LICENSE && \
    chmod -Rf +x monero/*

# Runtime stage
FROM debian:stable-slim

COPY --from=builder /app/monero/* /usr/bin/

# Create monero user
RUN addgroup --gid 1000 monero && \
    adduser --system --disabled-password --uid 1000 --ingroup monero monero && \
    mkdir -p /home/monero/.bitmonero && \
    chown -Rf monero:monero /home/monero

# Contains the blockchain
VOLUME /home/monero/.bitmonero

EXPOSE 18080
EXPOSE 18081
EXPOSE 18082

# switch to user monero
USER monero

# Healthcheck
HEALTHCHECK --interval=1m --timeout=30s CMD monerod status || exit 1

ENTRYPOINT ["monerod"]
CMD ["--data-dir=/home/monero/.bitmonero", "--non-interactive", "--prune-blockchain", "--no-igd", "--p2p-bind-ip=0.0.0.0", "--p2p-bind-port=18080", "--rpc-bind-ip=0.0.0.0", "--rpc-bind-port=18081", "--zmq-rpc-bind-ip=0.0.0.0", "--zmq-rpc-bind-por=18082", "--confirm-external-bind", "--add-priority-node=p2pmd.xmrvsbeast.com:18080", "--add-priority-node=nodes.hashvault.pro:18080", "--disable-dns-checkpoints", "--enable-dns-blocklist"]