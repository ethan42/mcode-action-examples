FROM debian:buster-slim as builder1
RUN apt-get update && \
    apt-get install -y gcc make libc6-dbg && \
    rm -rf /var/lib/apt/lists/*
COPY mayhemit.c .
RUN gcc mayhemit.c -o /mayhemit

FROM debian:10-slim as builder2
RUN apt-get update && apt-get install -y build-essential wget libc6-dbg
WORKDIR /build
RUN wget https://download.lighttpd.net/lighttpd/releases-1.4.x/lighttpd-1.4.15.tar.gz \
   && tar xf lighttpd-1.4.15.tar.gz \
   && cd /build/lighttpd-1.4.15 \
   && CFLAGS=-g ./configure --without-bzip2 && CFLAGS=-g make && make install \
   && mkdir /www && echo "lighttpd 1.4.15 running!" > /www/index.html
COPY lighttpd.conf /usr/local/etc
WORKDIR /
COPY testsuite /testsuite
CMD ["/usr/local/sbin/lighttpd","-D", "-f","/usr/local/etc/lighttpd.conf"]
EXPOSE 80

FROM debian:10-slim
RUN apt-get update && apt-get install -y  --no-install-recommends libc6-dbg
# Don't set workdir! corpus is relative to /
COPY testsuite /testsuite
COPY --from=builder1 /mayhemit /mayhemit
COPY --from=builder2 /usr/local /usr/local
RUN mkdir /www && echo "lighttpd 1.4.15 running!" > /www/index.html
EXPOSE 80
