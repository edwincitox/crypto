# buildx multiarch for linux
docker buildx build --platform linux/amd64,linux/arm64 -t edwincitox/zephyrd:latest -t edwincitox/zephyrd:1.0.4-built --push .

docker buildx build --platform linux/amd64,linux/arm64 -t edwincitox/monerod:latest -t edwincitox/monerod:0.18.3.3-built --push .

# monerod
docker build -t edwincitox/monerod:latest .
docker run -d -v "D:\monerod:/home/monero/.bitmonero" edwincitox/monerod:latest

# zephyrd
docker build -t edwincitox/zephyrd:latest .
docker run -d -v "D:\zephyrd:/home/zephyr/.bitzephyr" edwincitox/zephyrd:latest