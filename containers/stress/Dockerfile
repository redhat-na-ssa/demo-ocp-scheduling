# builder
FROM docker.io/library/fedora:latest AS build

ARG STRESS_VERSION='0.19.02'

# hadolint ignore=DL3018
RUN yum -y install \
      git curl \
      libxcrypt-devel  \
      gcc g++ eigen3-devel glibc-static Judy-devel keyutils-libs-devel libacl-devel libaio-devel libatomic-static libattr-devel libbsd-devel libcap-devel libgbm-devel libglvnd-core-devel libglvnd-devel libjpeg-devel libmd-devel libX11-devel libXau-devel libxcb-devel lksctp-tools-devel mpfr-devel xorg-x11-proto-devel xxhash-devel zlib-devel

WORKDIR /tmp
RUN git clone --depth 1 --branch V${STRESS_VERSION} https://github.com/ColinIanKing/stress-ng stress-ng

WORKDIR /tmp/stress-ng

RUN mkdir build && \
    rm -rf configs config.h && \
    VERBOSE=1 make -f Makefile.config -j $(nproc)

RUN VERBOSE=1 STATIC=1 make -j $(nproc) && \
    strip stress-ng && \
    make DESTDIR=build/ install

WORKDIR /tmp/stress-ng/build

# runtime
FROM registry.access.redhat.com/ubi8/ubi:latest as runtime

# hadolint ignore=DL3018
RUN dnf install curl
      yum clean all && \
      rm -rf /var/cache/yum/*

# setup: stress
COPY --from=build stress-ng/install-root/usr/bin/stress-ng /usr/local/bin

USER 1001

WORKDIR /tmp

ENTRYPOINT ["/usr/local/bin/stress-ng"]
CMD ["--help"]

### https://github.com/opencontainers/image-spec/blob/main/annotations.md
LABEL \
      org.opencontainers.image.description.vendor="stress-ng" \
      org.opencontainers.image.description="A container used to stress your system" \
      org.opencontainers.image.source="https://github.com/redhat-na-ssa/demo-ocp-scheduling"
