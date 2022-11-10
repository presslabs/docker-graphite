FROM buildpack-deps as build
ENV GO_CARBON_VERSION=0.16.2

RUN set -ex \
    && wget -O/go-carbon_amd64.deb https://github.com/go-graphite/go-carbon/releases/download/v${GO_CARBON_VERSION}/go-carbon_${GO_CARBON_VERSION}_amd64.deb

FROM debian:stretch-slim

RUN set -ex \
    && addgroup --system --gid 105 graphite \
    && adduser --system --uid 105 --gid 105 --home /opt/graphite --no-create-home --shell /bin/false --gecos "Graphite User" graphite

COPY --from=build /go-carbon_amd64.deb .
RUN set -ex \
    && dpkg -i go-carbon_amd64.deb \
    && rm go-carbon_amd64.deb


COPY opt/graphite /opt/graphite

USER graphite
WORKDIR /opt/graphite
ENTRYPOINT ["go-carbon"]
CMD ["-config=/opt/graphite/go-carbon/go-carbon.conf", "-daemon=false"]
