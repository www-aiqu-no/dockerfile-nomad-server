ARG BASE_NAME
ARG BASE_VERSION

FROM ${BASE_NAME:-alpine}:${BASE_VERSION:-3.9}
MAINTAINER Rune Rænneseth <rune.ronneseth@aiqu.no>

# This is the release of Nomad to pull in
ARG NOMAD_VERSION
ENV NOMAD_VERSION      ${NOMAD_VERSION:-"0.10.2"}
ENV HASHICORP_RELEASES "https://releases.hashicorp.com"

# This is the release of GLIBC to pull in
ARG GLIBC_VERSION
ENV GLIBC_VERSION  ${GLIBC_VERSION:-"2.30-r0"}
ENV GLIBC_RELEASES "https://github.com/andyshinn/alpine-pkg-glibc/releases"

# Create a nomad user and group first so the IDs get set the same way, even as
# the rest of this may change over time.
RUN set -eux \
  && addgroup nomad \
  && adduser -S -G nomad nomad

# Set up glibc
RUN set -eux \
  && cd /tmp \
  && apk --update add --no-cache --virtual .glibc-deps curl \
  && curl -LO ${GLIBC_RELEASES}/download/${GLIBC_VERSION}/glibc-${GLIBC_VERSION}.apk \
  && apk add --allow-untrusted /tmp/glibc-${GLIBC_VERSION}.apk \
  && rm -rf /tmp/glibc-${GLIBC_VERSION}.apk /var/cache/apk/* \
  &&apk del .glibc-deps

# Set up nomad
RUN set -eux \
  && apk --update add --no-cache --virtual .nomad-deps gnupg curl \
  && cd /tmp \
  && curl -LO ${HASHICORP_RELEASES}/nomad/${NOMAD_VERSION}/nomad_${NOMAD_VERSION}_linux_amd64.zip \
  && curl -LO ${HASHICORP_RELEASES}/nomad/${NOMAD_VERSION}/nomad_${NOMAD_VERSION}_SHA256SUMS \
  && curl -LO ${HASHICORP_RELEASES}/nomad/${NOMAD_VERSION}/nomad_${NOMAD_VERSION}_SHA256SUMS.sig \
  && export GNUPGHOME="$(mktemp -d)" \
  && gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 91A6E7F85D05C65630BEF18951852D87348FFC4C \
  && gpg --batch --verify nomad_${NOMAD_VERSION}_SHA256SUMS.sig nomad_${NOMAD_VERSION}_SHA256SUMS \
  && grep nomad_${NOMAD_VERSION}_linux_amd64.zip nomad_${NOMAD_VERSION}_SHA256SUMS | sha256sum -c \
  && unzip -d /bin nomad_${NOMAD_VERSION}_linux_amd64.zip \
  && chmod +x /bin/nomad \
  && rm -rf "$GNUPGHOME" nomad_${NOMAD_VERSION}_linux_amd64.zip nomad_${NOMAD_VERSION}_SHA256SUMS nomad_${NOMAD_VERSION}_SHA256SUMS.sig \
  && apk del .nomad-deps

# Set up certificates & tools
RUN set -eux \
  && apk --update add --no-cache ca-certificates openssl su-exec dumb-init \
  && update-ca-certificates

RUN set -eux \
  && mkdir -p /nomad/data \
  && mkdir -p /nomad/config \
  && chown -R nomad:nomad /nomad

# Expose the nomad data directory as a volume since there's mutable state in there.
VOLUME /nomad/data

# HTTP API: This is used by clients and servers to serve the HTTP API.
# TCP only.
EXPOSE 4646

# RPC: This is used for internal RPC communication between client agents and
# servers, and for inter-server traffic.
# TCP only.
EXPOSE 4647

# Serf WAN: This is used by servers to gossip both over the LAN and WAN to
# other servers. It isn't required that Nomad clients can reach this address.
# TCP and UDP.
EXPOSE 4648 4648/udp

COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN ["chmod","+x","/usr/local/bin/docker-entrypoint.sh"]
ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]

# By default you'll get an insecure single-node development server that exposes
# a web UI and HTTP endpoints, and bootstraps itself.
CMD ["agent", "-server", "-bind=0.0.0.0","-bootstrap-expect=1"]
