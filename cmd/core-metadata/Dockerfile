#
# Copyright (c) 2018
# Dell
# Cavium
#
# SPDX-License-Identifier: Apache-2.0
#

FROM golang:1.13-alpine AS builder

ENV GO111MODULE=on
WORKDIR /go/src/github.com/edgexfoundry/edgex-go

# The main mirrors are giving us timeout issues on builds periodically.
# So we can try these.

RUN sed -e 's/dl-cdn[.]alpinelinux.org/nl.alpinelinux.org/g' -i~ /etc/apk/repositories

RUN apk update && apk add make git

COPY go.mod .
# COPY go.sum .

RUN go mod download

COPY . .
RUN make cmd/core-metadata/core-metadata

FROM scratch

LABEL license='SPDX-License-Identifier: Apache-2.0' \
      copyright='Copyright (c) 2018: Dell, Cavium'

ENV APP_PORT=48082
#expose meta data port
EXPOSE $APP_PORT

WORKDIR /
COPY --from=builder /go/src/github.com/edgexfoundry/edgex-go/cmd/core-metadata/Attribution.txt /
COPY --from=builder /go/src/github.com/edgexfoundry/edgex-go/cmd/core-metadata/core-metadata /
COPY --from=builder /go/src/github.com/edgexfoundry/edgex-go/cmd/core-metadata/res/docker/configuration.toml /res/docker/configuration.toml
ENTRYPOINT ["/core-metadata","-cp=consul.http://edgex-core-consul:8500","--registry","--profile=docker","--confdir=/res"]
