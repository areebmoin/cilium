# Copyright 2020-2021 Authors of Cilium
# SPDX-License-Identifier: Apache-2.0

ARG GOLANG_IMAGE=docker.io/library/golang:1.16.0-alpine3.13@sha256:f3f90f4d30866c1bdae90012b506bd5e557ce27ccd2510ed30a011c44c1affc8
ARG ALPINE_IMAGE=docker.io/library/alpine:3.13.1@sha256:08d6ca16c60fe7490c03d10dc339d9fd8ea67c6466dea8d558526b1330a85930

FROM ${GOLANG_IMAGE} as builder

RUN apk add --no-cache \
  curl \
  bash \
  make \
  && true

COPY install-protoplugins.sh /tmp/install-protoplugins.sh
RUN /tmp/install-protoplugins.sh

#------------------------------------------------------------------------------

FROM ${ALPINE_IMAGE}

WORKDIR /proto

RUN apk add --no-cache \
  curl \
  bash \
  make \
  && true

COPY install-glibc.sh /tmp/install-glibc.sh
RUN /tmp/install-glibc.sh

COPY install-protoc.sh /tmp/install-protoc.sh
RUN /tmp/install-protoc.sh

COPY --from=builder /proto/protoc-gen-go .
COPY --from=builder /proto/protoc-gen-go-grpc .
COPY --from=builder /proto/protoc-gen-go-json .

COPY Makefile.docker ./Makefile

WORKDIR /source

CMD ["make", "-f", "/proto/Makefile"]
