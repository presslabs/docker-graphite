FROM buildpack-deps as build
ENV GO_CARBON_VERSION=v0.13.0

RUN set -ex \
    && wget -O/go-carbon https://github.com/lomik/go-carbon/releases/download/${GO_CARBON_VERSION}/go-carbon-linux-amd64 \
    && chmod +x /go-carbon


FROM debian:stretch-slim
RUN set -ex \
    && addgroup --system --gid 105 graphite \
    && adduser --system --uid 105 --gid 105 --home /opt/graphite --no-create-home --shell /bin/false --gecos "Graphite User" graphite
COPY --from=build /go-carbon /go-carbon
COPY conf/ /etc/go-carbon/
USER graphite
ENTRYPOINT ["/go-carbon"]
CMD ["-config=/etc/go-carbon/go-carbon.conf", "-daemon=false"]
