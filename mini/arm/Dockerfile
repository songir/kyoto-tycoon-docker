FROM songir/kyoto-tycoon-centos7:apple-m1-latest as builder

MAINTAINER Qi, Chengri (Eason) <chengri.a.qi@rakuten.com>

FROM centos:7

RUN yum -y install openssl* zlib-devel lzo-devel

COPY --from=builder /usr/local/bin/kc* /usr/local/bin/
COPY --from=builder /usr/local/bin/kt* /usr/local/bin/
COPY --from=builder /usr/local/bin/msgpack-idl /usr/local/bin

COPY --from=builder /usr/local/include/* /usr/local/include/
COPY --from=builder /usr/local/libexec/* /usr/local/libexec/
COPY --from=builder /usr/local/share/msgpack-idl /usr/local/share/

COPY --from=builder /usr/local/lib /usr/local/lib


RUN grep "/usr/local/lib" /etc/ld.so.conf || echo "/usr/local/lib" >> /etc/ld.so.conf && ldconfig
RUN mkdir -p /data/db

ENTRYPOINT ["ktserver"]

EXPOSE 1978
