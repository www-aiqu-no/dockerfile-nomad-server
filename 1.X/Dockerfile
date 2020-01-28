# Runtime image version
ARG BASE_NAME="ubuntu"
ARG BASE_VERSION="20.04"

# External package versions
ARG NOMAD_VERSION="0.10.2"

# ------------------------------------------------------------------------------

FROM alpine:3 as helper

ARG NOMAD_VERSION
ENV NOMAD_VERSION      ${NOMAD_VERSION}
ENV HASHICORP_RELEASES "https://releases.hashicorp.com"

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
  && mkdir -p /out \
  && unzip -d /out nomad_${NOMAD_VERSION}_linux_amd64.zip \
  && chmod +x /out/nomad \
  && rm -rf "$GNUPGHOME" nomad_${NOMAD_VERSION}_linux_amd64.zip nomad_${NOMAD_VERSION}_SHA256SUMS nomad_${NOMAD_VERSION}_SHA256SUMS.sig \
  && apk del .nomad-deps

# ------------------------------------------------------------------------------

FROM ${BASE_NAME}:${BASE_VERSION}
MAINTAINER Rune Rønneseth <rune.ronneseth@aiqu.no>

ARG NOMAD_VERSION
ENV NOMAD_VERSION ${NOMAD_VERSION}

# Create a nomad user and group first so the IDs get set the same way, even as
# the rest of this may change over time.
RUN set -eux \
  && addgroup nomad \
  && adduser --shell /usr/bin/bash --ingroup nomad nomad

# Create folders
RUN set -eux \
  && mkdir -p /nomad/data \
  && mkdir -p /nomad/config \
  && chown -R nomad:nomad /nomad

# Install tools
RUN set -eux \
  && apt-get update \
  && apt-get install gosu dumb-init \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

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

# Add downloaded binary from helper-image
COPY --from=helper /out/nomad /usr/local/bin/nomad

# Add entrypoint & allow to execute
COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN set -eux \
  && chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]

# By default you'll get an insecure single-node development server that
# exposes a web UI and HTTP endpoints, and bootstraps itself.
CMD ["agent", "-server", "-bind", "0.0.0.0", "-bootstrap-expect","1"]
