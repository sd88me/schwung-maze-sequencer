# Cross-compile toolchain for the Ableton Move (ARM64) DSP plugins.
FROM debian:bookworm

RUN apt-get update && apt-get install -y \
        gcc-aarch64-linux-gnu \
        binutils-aarch64-linux-gnu \
        file \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /build
