# monerod
docker build -t edwincitox/monerod:latest .
docker run -d -v "D:\monerod:/home/monero/.bitmonero" edwincitox/monerod:latest

# zephyrd
docker build -t edwincitox/zephyrd:latest .
docker run -d -v "D:\zephyrd:/home/zephyr/.bitzephyr" edwincitox/zephyrd:latest
