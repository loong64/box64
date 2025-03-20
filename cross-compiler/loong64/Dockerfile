FROM ubuntu:20.04 AS builde-box64

RUN apt-get update \
    && apt-get install -y software-properties-common \
    && apt-get install -y \
         cmake \
         git \
         g++ \
         curl \
         make \
         xz-utils

RUN curl -L https://github.com/loongson/build-tools/releases/download/2024.08.08/x86_64-cross-tools-loongarch64-binutils_2.43-gcc_14.2.0-glibc_2.40.tar.xz | tar xJf - -C /opt

ENV PATH=/opt/cross-tools/bin:$PATH

ARG VERSION
ARG WORKDIR=/opt/box64

WORKDIR ${WORKDIR}

RUN git clone --depth 1 --branch ${VERSION} https://github.com/ptitSeb/box64 ${WORKDIR}

RUN mkdir -p build \
    && cd build \
    && cmake .. -DCMAKE_C_COMPILER=loongarch64-unknown-linux-gnu-gcc -D LARCH64=1 -D CMAKE_BUILD_TYPE=RelWithDebInfo -D BOX32=ON -D BOX32_BINFMT=ON \
    && make -j$(nproc)

RUN mkdir -p dist \
    && cp -f build/box64 dist/box64 \
    && cd dist \
    && tar -czf box64-linux-loong64.tar.gz box64 \
    && sha256sum box64-linux-loong64.tar.gz > box64-linux-loong64.tar.gz.sha256 \
    && mv box64 box64-linux-loong64

FROM ubuntu:20.04

COPY --from=builde-box64 /opt/box64/dist /opt/box64

VOLUME /dist

CMD cp -rf /opt/box64/* /dist/