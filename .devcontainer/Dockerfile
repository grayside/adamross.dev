# Args used in container tags:
ARG NODE_VERSION=26

# This is the official docker image for Vale, defined in https://github.com/errata-ai/vale/blob/v3/Dockerfile
FROM jdkato/vale:latest AS vale

FROM mcr.microsoft.com/devcontainers/javascript-node:${NODE_VERSION}

# VARIANT can be either 'hugo' for the standard version or 'hugo_extended' for the extended version.
ARG VARIANT=hugo_extended
# VERSION can be either 'latest' or a specific version number
ARG VERSION=latest
# Pinned version for Dart Sass standalone binary
ARG DART_SASS_VERSION=1.80.6

# Download Hugo
RUN apt-get update && apt-get install -y ca-certificates openssl git curl && \
    rm -rf /var/lib/apt/lists/* && \
    case ${VERSION} in \
    latest) \
    export VERSION=$(curl -s https://api.github.com/repos/gohugoio/hugo/releases/latest | grep "tag_name" | awk '{print substr($2, 3, length($2)-4)}') ;;\
    esac && \
    echo ${VERSION} && \
    case $(uname -m) in \
    aarch64) \
    export ARCH=ARM64 ;; \
    *) \
    export ARCH=64bit ;; \
    esac && \
    echo ${ARCH} && \
    wget -O ${VERSION}.tar.gz https://github.com/gohugoio/hugo/releases/download/v${VERSION}/${VARIANT}_${VERSION}_Linux-${ARCH}.tar.gz && \
    tar xf ${VERSION}.tar.gz && \
    mv hugo /usr/bin/hugo && \
    rm -rf ${VERSION}.tar.gz

# Download Dart Sass (standalone binary required by Hugo for the embedded Sass protocol)
RUN set -eux; \
    case $(uname -m) in \
    aarch64) \
    SASS_ARCH="linux-arm64" ;; \
    *) \
    SASS_ARCH="linux-x64" ;; \
    esac && \
    mkdir -p /usr/local/lib && \
    curl -fsSL "https://github.com/sass/dart-sass/releases/download/${DART_SASS_VERSION}/dart-sass-${DART_SASS_VERSION}-${SASS_ARCH}.tar.gz" \
        | tar -xz -C /usr/local/lib && \
    ln -sf /usr/local/lib/dart-sass/sass /usr/local/bin/sass && \
    sass --embedded --version

# Hugo dev server port
EXPOSE 1313

# [Optional] Uncomment this section to install additional OS packages you may want.
#
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment if you want to install more global node packages
# RUN sudo -u node npm install -g <your-package-list-here>

# Using the npm installation wrapper for installing taskfile.dev
# https://taskfile.dev/
RUN sudo -u node npm install -g \
  @go-task/cli \
  firebase-tools

COPY --from=vale /bin/vale /usr/bin/vale