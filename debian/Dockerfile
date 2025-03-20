FROM ghcr.io/loong64/debian:trixie-slim AS builde-box64
ARG TARGETARCH

ARG DEPENDENCIES="  \
        devscripts  \
        equivs      \
        git"

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update \
    && apt-get install -y ${DEPENDENCIES}

ARG VERSION
ARG WORKDIR=/opt/box64

WORKDIR ${WORKDIR}

RUN git clone --depth 1 --branch ${VERSION} https://github.com/ptitSeb/box64

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update \
    && cd box64 \
    && mk-build-deps -t "apt-get -o Debug::pkgProblemResolver=yes --no-install-recommends -y" -i debian/control \
    && rm -f box64-build-deps_*.{deb,buildinfo,changes}

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    echo "deb http://deb.debian.org/debian sid main" > /etc/apt/sources.list \
    && dpkg --add-architecture amd64 \
    && dpkg --add-architecture i386 \
    && apt-get update \
    && apt-get install -y libc6:amd64 libc6:i386

ENV DEB_BUILD_OPTIONS=nocheck

COPY box64.patch /opt/box64.patch
RUN cd box64 \
    && git apply /opt/box64.patch \
    && dpkg-buildpackage -b -rfakeroot -us -uc \
    && cd .. \
    && rm -rf box64

FROM ghcr.io/loong64/debian:trixie-slim
ARG TARGETARCH

COPY --from=builde-box64 /opt/box64 /opt/box64

VOLUME /dist

CMD cp -rf /opt/box64/* /dist/