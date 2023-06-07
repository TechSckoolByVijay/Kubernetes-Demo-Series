FROM ubuntu:latest
RUN apt-get update && apt-get install -y curl
CMD while true; do curl https://www.google.com/ >> /shared-drive/test.log; sleep 60; done