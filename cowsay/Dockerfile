FROM ubuntu:latest

MAINTAINER Zhenglai Zhang <zhenglaizhang@hotmail.com>
RUN apt-get update && apt-get install -y cowsay fortune

COPY entrypoint.sh /

ENTRYPOINT ["/entrypoint.sh"]
