FROM alpine:edge

MAINTAINER Alex Masterov <alex.masterow@gmail.com>
LABEL repository.hub="alexmasterov/alpine-memcached" \
      repository.url="https://github.com/AlexMasterov/dockerfiles"

ARG MEMCACHED_VERSION=1.4.36

RUN \
  apk add --update \
    tini \

  && addgroup -g 82 -S memcache \
  && adduser -u 82 -S -D -h /var/cache/memcache -s /sbin/nologin -G memcache memcache

RUN \
  apk add --virtual .memcached-build-dependencies \
    curl \
    g++ \
    gcc \
    libevent-dev \
    make \
  && apk add --virtual .memcached-runtime-dependencies \
    libevent \

  # Memcached
  && MEMCACHED_SOURCE="https://memcached.org/files/memcached-${MEMCACHED_VERSION}.tar.gz" \
  && curl -fSL --connect-timeout 30 ${MEMCACHED_SOURCE} | tar xz -C /tmp \
  && cd /tmp/memcached-${MEMCACHED_VERSION} \

  && ./configure \
    CFLAGS="-O2 -march=native" \
  && make -j "$(getconf _NPROCESSORS_ONLN)" \
  && make install \

  # Removing build dependencies, clean temporary files
  && apk del .memcached-build-dependencies \
  && rm -rf /var/cache/apk/* /var/tmp/* /tmp/*

ENTRYPOINT ["tini", "--"]
CMD ["memcached"]
