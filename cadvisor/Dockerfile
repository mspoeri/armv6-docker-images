FROM --platform="linux/arm/v6" golang:1.20-alpine AS builder
ARG VERSION

WORKDIR /tmp/build

RUN apk update \
 && apk add build-base make git bash gcc \
 && git clone https://github.com/google/cadvisor.git /tmp/build

ENV GOOS=linux
ENV GOARCH=arm
ENV GOARM=6
ENV GO_CGO_ENABLED=0
ENV CGO_ENABLED=0

RUN git fetch --tags
RUN git checkout $VERSION
RUN go env -w CGO_ENABLED=0 \
 && go env -w GOOS=linux \
 && go env -w GOARCH=arm \
 && go env -w GOARM=6 \
 && go mod download

RUN make build

FROM --platform="linux/arm/v6" scratch

COPY --from=builder /tmp/build/_output/cadvisor /opt/cadvisor

EXPOSE 8080

ENTRYPOINT ["/opt/cadvisor", "-logtostderr"]
