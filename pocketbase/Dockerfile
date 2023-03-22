# syntax=docker/dockerfile:labs

FROM alpine:3.17

ARG PB_VERSION=0.13.4
ARG PB_CHECKSUM="08a85cdc8a9df3b3583295644c8853bc645db37f976a9aa3620ea224debf3472"

ENV PB_PORT 8090
ENV PB_ENCRYPTION_KEY ""

RUN apk add --no-cache \
  unzip=6.0-r13 \
  ca-certificates=20220614-r4 \
  curl=7.88.1-r1

ADD --checksum=sha256:${PB_CHECKSUM} https://github.com/pocketbase/pocketbase/releases/download/v${PB_VERSION}/pocketbase_${PB_VERSION}_linux_amd64.zip /tmp/pb.zip

RUN addgroup -g 1000 pbadmin \
  && adduser -u 1000 -G pbadmin -s /usr/sbin/nologin -D pbadmin \
  && unzip /tmp/pb.zip -d /pb/ \
  && chown -R pbadmin:pbadmin /pb && chmod 750 /pb/pocketbase \
  && mkdir -p /var/lib/pocketbase/data && chown -R pbadmin:pbadmin /var/lib/pocketbase/data

EXPOSE ${PB_PORT}

STOPSIGNAL SIGTERM

VOLUME [ "/var/lib/pocketbase/data" ]

HEALTHCHECK --interval=90s --timeout=60s --start-period=10s --retries=5 CMD curl -f http://0.0.0.0:${PB_PORT}/api/health || exit 1

USER pbadmin

CMD ["/bin/sh", "-c", "/pb/pocketbase serve --dir=/var/lib/pocketbase/data --encryptionEnv=PB_ENCRYPTION_KEY --http=0.0.0.0:${PB_PORT}"]
