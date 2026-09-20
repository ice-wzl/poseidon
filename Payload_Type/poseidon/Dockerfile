FROM itsafeaturemythic/mythic_go_macos:latest

ARG TARGETARCH

RUN chmod +x /go/bin/garble
RUN garble version

WORKDIR /

RUN if [ "$TARGETARCH" = "arm64" ]; then \
    echo "Installing ARM-specific packages" && \
    # Add your ARM-specific commands here; \
    wget https://github.com/indygreg/apple-platform-rs/releases/download/apple-codesign%2F0.29.0/apple-codesign-0.29.0-aarch64-unknown-linux-musl.tar.gz && \
    gunzip apple-codesign-0.29.0-aarch64-unknown-linux-musl.tar.gz && \
    tar -xvf apple-codesign-0.29.0-aarch64-unknown-linux-musl.tar && \
    mv apple-codesign-0.29.0-aarch64-unknown-linux-musl/rcodesign /rcodesign && \
    chmod +x /rcodesign; \
    elif [ "$TARGETARCH" = "amd64" ]; then \
    echo "Installing AMD-specific packages" && \
    # Add your AMD-specific commands here; \
    wget -o /rcodesign.tar.gz https://github.com/indygreg/apple-platform-rs/releases/download/apple-codesign%2F0.29.0/apple-codesign-0.29.0-x86_64-unknown-linux-musl.tar.gz && \
    gunzip apple-codesign-0.29.0-x86_64-unknown-linux-musl.tar.gz && \
    tar -xvf apple-codesign-0.29.0-x86_64-unknown-linux-musl.tar && \
    mv apple-codesign-0.29.0-x86_64-unknown-linux-musl/rcodesign /rcodesign && \
    chmod +x /rcodesign; \
    fi

ARG UPX_VERSION=5.2.1

RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) UPX_SHA256="402162aad30af47e60dbd767fb2e64ca394ace9727ba1f40283641f1d1b91657" ;; \
        arm64) UPX_SHA256="a72d112c5970a904a31da0b9c84f919bc16b9a311787c12245508544a78c7d36" ;; \
        *) echo "Unsupported container architecture: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    UPX_ARCHIVE="upx-${UPX_VERSION}-${TARGETARCH}_linux.tar.xz"; \
    wget -O "/tmp/${UPX_ARCHIVE}" \
        "https://github.com/upx/upx/releases/download/v${UPX_VERSION}/${UPX_ARCHIVE}"; \
    echo "${UPX_SHA256}  /tmp/${UPX_ARCHIVE}" | sha256sum -c -; \
    tar -xJf "/tmp/${UPX_ARCHIVE}" -C /tmp; \
    install -m 0755 \
        "/tmp/upx-${UPX_VERSION}-${TARGETARCH}_linux/upx" \
        /usr/local/bin/upx; \
    rm -rf "/tmp/${UPX_ARCHIVE}" \
        "/tmp/upx-${UPX_VERSION}-${TARGETARCH}_linux"

RUN upx --version

WORKDIR /Mythic/

COPY [".", "."]

RUN make build

RUN apt-get install g++-x86-64-linux-gnu libc6-dev-amd64-cross -y

CMD make run