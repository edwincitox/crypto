# Builder stage
FROM debian:stable-slim AS builder

ARG latest_release=v1.0.4

RUN apt-get update && \
    apt-get full-upgrade -y && \
    apt-get install --no-install-recommends -y build-essential ca-certificates ccache cmake doxygen git graphviz libboost-all-dev libboost-chrono-dev libboost-date-time-dev libboost-filesystem-dev libboost-locale-dev libboost-program-options-dev libboost-regex-dev libboost-serialization-dev libboost-system-dev libboost-thread-dev libexpat1-dev libgtest-dev libhidapi-dev liblzma-dev libnorm-dev libpgm-dev libprotobuf-dev libreadline6-dev libsodium-dev libssl-dev libudev-dev libunbound-dev libunwind8-dev libusb-1.0-0-dev libzmq3-dev pkg-config protobuf-compiler python3 qttools5-dev-tools sudo && \
    cd /usr/src/gtest && \
    sudo cmake . && \
    sudo make && \
    sudo mv lib/libg* /usr/lib/
WORKDIR /build
RUN git clone --recursive https://github.com/ZephyrProtocol/zephyr && \
    cd zephyr && \
    git submodule init && \
    git submodule sync && \
    git submodule update && \
    git checkout ${latest_release} && \
    git submodule update && \
    make && \
    mkdir -p /app/ && \
    mv /build/zephyr/build/Linux/_HEAD_detached_at_${latest_release}_/release/bin/* /app/  && \
    chmod -Rf +x /app/

# Runtime stage
FROM debian:stable-slim

COPY --from=builder /app/* /usr/bin/

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