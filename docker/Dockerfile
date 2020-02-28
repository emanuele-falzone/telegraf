FROM golang:1.12.14-stretch AS BUILD

RUN apt-get update \
	&& apt-get upgrade -y \
	&& apt-get install -y --no-install-recommends --no-install-suggests \
	        ca-certificates \
	        git mercurial subversion bzr \
	&& update-ca-certificates \
	&& curl -fL -o /usr/local/bin/dep \
	     https://github.com/golang/dep/releases/download/v0.5.4/dep-linux-amd64 \
	&& chmod +x /usr/local/bin/dep


WORKDIR /go/src/github.com/influxdata

RUN git clone https://github.com/emanuele-falzone/telegraf.git

WORKDIR /go/src/github.com/influxdata/telegraf

RUN git checkout avro

RUN dep ensure -vendor-only

RUN rm -rf vendor/github.com/influxdata/telegraf/

RUN go build -ldflags " -X main.commit=9b7d6acc -X main.branch=avro" ./cmd/telegraf

FROM debian:stretch

COPY --from=BUILD /go/src/github.com/influxdata/telegraf/telegraf /usr/local/bin

WORKDIR /config

CMD ["telegraf", "--config", "/config/telegraf.conf"]
