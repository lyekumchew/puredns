FROM alpine:edge as builder

LABEL maintainer="AnyMoe https://github.com/anymoe"

RUN apk add --no-cache git alpine-sdk build-base openssl-dev linux-headers rsync &&\
    git clone --depth=1 https://github.com/pymumu/smartdns.git &&\
    mkdir -p /smartdns/rootfs/usr/bin && cp -R /smartdns/etc /smartdns/rootfs/

RUN cd /smartdns &&\
    make all &&\
    cp src/smartdns rootfs/usr/bin/

FROM alpine:edge

LABEL maintainer="AnyMoe https://github.com/anymoe"

ENV TZ='Asia/Hong_Kong'

COPY --from=builder /smartdns/rootfs/ /

RUN apk add --no-cache ca-certificates openssl libc6-compat curl bash;\
    mkdir -p /var/log/smartdns;\
    touch /var/log/smartdns.log; \
    mkdir -p /var/run/smartdns; \
    adduser --disabled-password smartdns &&\
    chmod 775 /etc/smartdns /var/log/smartdns /var/run/smartdns /var/log/smartdns.log;\
    chgrp smartdns /etc/smartdns /var/log/smartdns /var/run/smartdns /var/log/smartdns.log;\
    chown smartdns /etc/smartdns /var/log/smartdns /var/run/smartdns /var/log/smartdns.log

VOLUME ["/etc/smartdns", "/var/log/smartdns"]

USER smartdns

EXPOSE 53

ENTRYPOINT ["/usr/bin/smartdns", "-p", "/var/run/smartdns/smartdns.pid", "-f"]

CMD ["-f", "-c", "/etc/smartdns/smartdns.conf"]
