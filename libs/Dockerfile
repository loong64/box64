FROM almalinux:9 AS builde-bundle
ARG TARGETARCH

RUN dnf install -y rpm-build git dnf-plugins-core zstd

ARG VERSION
ARG WORKDIR=/opt/box64

WORKDIR ${WORKDIR}
RUN git clone --depth 1 --branch ${VERSION} https://github.com/ptitSeb/box64 ${WORKDIR}

RUN bash box64-bundle-x86-libs.sh

FROM almalinux:9
ARG TARGETARCH

COPY --from=builde-bundle /opt/box64/box64-bundle-x86-* /opt/box64/

VOLUME /dist

CMD cp -rf /opt/box64/* /dist/