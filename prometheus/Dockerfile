FROM --platform="arm64" busybox as prep
ARG VERSION="2.48.1"

RUN wget -O prometheus.tar.gz \
    "https://github.com/prometheus/prometheus/releases/download/v${VERSION}/prometheus-${VERSION}.linux-armv6.tar.gz"

RUN mkdir -p /prometheus && tar -xzf prometheus.tar.gz -C /prometheus --strip-components=1

RUN mkdir -p /etc/prometheus && mv /prometheus/prometheus.yml /etc/prometheus/prometheus.yml
RUN chown -R 65532:65532 /etc/prometheus


FROM --platform="arm64" gcr.io/distroless/base:nonroot as base

FROM scratch

LABEL org.opencontainers.image.source https://github.com/mspoeri/prometheus-armv6-docker

COPY --from=base / /
COPY --from=prep /prometheus/prometheus             /bin/prometheus
COPY --from=prep /etc/prometheus/                   /etc/prometheus/
COPY --from=prep /prometheus/LICENSE                /LICENSE
COPY --from=prep /prometheus/NOTICE                 /NOTICE

WORKDIR /prometheus

USER        65532:65532
EXPOSE      9090
VOLUME      [ "/prometheus" ]
ENTRYPOINT  [ "/bin/prometheus" ]
CMD         [ "--config.file=/etc/prometheus/prometheus.yml", \
              "--enable-feature=agent"]
