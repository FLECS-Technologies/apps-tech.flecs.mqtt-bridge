#
# Copyright 2021-2023 FLECS Technologies GmbH

FROM --platform=linux/amd64 flecspublic.azurecr.io/flecs-build AS builder

ARG ARCH
RUN [ ! -z "${ARCH}" ]

ADD app/ /usr/src/app

WORKDIR /usr/src/app

RUN cmake -B build/ -G Ninja -DARCH=${ARCH} -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/fs/local
RUN cmake --build build/
RUN cmake --build build/ --target install

FROM debian:bookworm-slim

COPY --from=builder /fs /usr/

RUN ldconfig

ENTRYPOINT ["/usr/bin/mqtt_bridge"]
