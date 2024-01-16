#
# Copyright 2021-2023 FLECS Technologies GmbH

FROM flecspublic.azurecr.io/flecs-build AS builder

ARG ARCH
ARG VERSION
ARG VARIANT

FROM debian:bookworm-slim

ARG ARCH
ARG VERSION
ARG VARIANT

RUN [ ! -z "${ARCH}" ]
RUN [ ! -z "${VERSION}" ]

COPY --from=builder /fs /

ENTRYPOINT ["/usr/bin/mqtt_bridge"]
