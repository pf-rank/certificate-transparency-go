# This Dockerfile builds a base image for the certificate-transparency-go CloudBuild integration testing.
# See https://hub.docker.com/_/golang for the set of golang base images.
FROM golang:1.24.4-bookworm@sha256:8faa537d0690017d8000e38e1ed9ed4c5990d40a3022ce2596a825c6c87f62c4 as ct_testbase

WORKDIR /testbase

ARG GOFLAGS=""
ENV GOFLAGS=$GOFLAGS

RUN apt-get update && apt-get -y install build-essential curl docker-compose lsof mariadb-client netcat-openbsd postgresql-client unzip wget xxd

RUN cd /usr/bin && curl -L -O https://github.com/jqlang/jq/releases/download/jq-1.7/jq-linux64 && mv jq-linux64 /usr/bin/jq && chmod +x /usr/bin/jq
RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(go env GOPATH)/bin v1.61.0
RUN mkdir protoc && \
    (cd protoc && \
    PROTOC_VERSION=3.20.1 && \
    PROTOC_ZIP="protoc-${PROTOC_VERSION}-linux-x86_64.zip" && \
    wget "https://github.com/google/protobuf/releases/download/v${PROTOC_VERSION}/${PROTOC_ZIP}" && \
    unzip -o ${PROTOC_ZIP} -d /usr/local bin/protoc && \
    unzip -o ${PROTOC_ZIP} -d /usr/local 'include/*' \
    )
ENV PATH /usr/local/bin:$PATH

COPY ./integration/test-runner.sh /testbase/

ENV GOPATH /go
ENV PATH $GOPATH/bin:/testbase/protoc/bin:$PATH

CMD /bin/bash -exc /testbase/test-runner.sh
