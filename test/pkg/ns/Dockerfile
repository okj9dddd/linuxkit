FROM linuxkit/alpine:5ce235f4fb55772e7f78871a70bfe26f774fe2b0 AS mirror
RUN mkdir -p /out/etc/apk && cp -r /etc/apk/* /out/etc/apk/
RUN apk add --no-cache --initdb -p /out \
    alpine-baselayout \
    busybox \
    iproute2 \
    jq \
    musl
RUN rm -rf /out/etc/apk /out/lib/apk /out/var/cache

FROM linuxkit/alpine:5ce235f4fb55772e7f78871a70bfe26f774fe2b0 AS build
RUN apk add --no-cache \
    build-base \
    git \
    go \
    musl-dev
ENV GOPATH=/go PATH=$PATH:/go/bin
ENV VIRTSOCK_COMMIT=f1e32d3189e0dbb81c0e752a4e214617487eb41f
RUN git clone https://github.com/linuxkit/virtsock.git /go/src/github.com/linuxkit/virtsock && \
    cd /go/src/github.com/linuxkit/virtsock && \
    git checkout $VIRTSOCK_COMMIT && \
    make bin/sock_stress.linux


FROM scratch
COPY --from=mirror /out/ /
COPY --from=build /go/src/github.com/linuxkit/virtsock/bin/sock_stress.linux /rootfs/sock_stress
COPY config.template.json *.sh /

LABEL org.mobyproject.config='{"pid": "host", "net":"host", "binds": ["/dev:/dev","/sys:/sys", "/usr/bin/runc:/usr/bin/runc"], "capabilities": ["all"]}'
