FROM debian:10.8

LABEL maintainer "Dennis Boeckmann <dev@dbck.de>"
LABEL description "GraalVM"

ENV DEBIAN_FRONTEND noninteractive

ARG BUILDPLATFORM
# Is linux/amd64 or linux/arm64
ARG TARGETPLATFORM

ENV BUILD_PLATFORM="${BUILDPLATFORM}"
ENV TARGET_PLATFORM="${TARGETPLATFORM}"
ENV GRAALVM_VERSION="21.0.0.2"
ENV PATH="/opt/graalvm/bin:${PATH}"
ENV JAVA_HOME="/opt/graalvm"

RUN echo "I am running on ${BUILD_PLATFORM} and building for ${TARGET_PLATFORM}"

# Prerequisites to install graalvm
RUN apt-get -qq update \
    && apt-get -qq install -y \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Prerequisites for native-image
RUN apt-get -qq update \
    && apt-get -qq install -y \
    build-essential \
    libz-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Install graalvm
# See: https://www.graalvm.org/docs/getting-started/linux/
# Download new versions: https://github.com/graalvm/graalvm-ce-builds/releases
RUN if [ "${TARGET_PLATFORM}" = "linux/arm64" ];then GRAALVM_PLATFORM=aarch64; else GRAALVM_PLATFORM=amd64; fi \
    && curl -LJ -o /tmp/graalvm.tar.gz https://github.com/graalvm/graalvm-ce-builds/releases/download/vm-${GRAALVM_VERSION}/graalvm-ce-java11-linux-${GRAALVM_PLATFORM}-${GRAALVM_VERSION}.tar.gz \
    && mkdir -p ${JAVA_HOME} \
    && tar -xzf /tmp/graalvm.tar.gz -C ${JAVA_HOME} --strip-components=1  \
    && rm /tmp/graalvm.tar.gz

# Install native-image
# https://www.graalvm.org/reference-manual/native-image/
RUN gu install native-image

COPY root/docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod 755 /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]