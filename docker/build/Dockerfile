#
# Docker image to generate deterministic, verifiable builds of Anchor programs.
# This must be run *after* a given ANCHOR_CLI version is published and a git tag
# is released on GitHub.
#

FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

ARG SOLANA_CLI
ARG ANCHOR_CLI

ENV HOME="/root"
ENV PATH="${HOME}/.cargo/bin:${PATH}"
ENV PATH="${HOME}/.local/share/solana/install/active_release/bin:${PATH}"
ENV PATH="${HOME}/.nvm/versions/node/v16.9.1/bin:${PATH}"

# Install base utilities.
RUN mkdir -p /workdir && mkdir -p /tmp && \
    apt-get update -qq && apt-get upgrade -qq && apt-get install -qq \
    build-essential git curl wget jq pkg-config python3-pip \
    libssl-dev libudev-dev

# Install rust.
RUN curl "https://sh.rustup.rs" -sfo rustup.sh && \
    sh rustup.sh -y && \
    rustup component add rustfmt clippy

# Install node / npm / yarn.
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
ENV NVM_DIR="${HOME}/.nvm"
RUN . $NVM_DIR/nvm.sh && \
    nvm install node --latest-npm && \
    nvm use node && \
    nvm alias default node && \
    npm install -g yarn

# Install Solana tools.
RUN sh -c "$(curl -sSfL https://release.solana.com/${SOLANA_CLI}/install)"

# Install anchor.
RUN cargo install --git https://github.com/project-serum/anchor --tag ${ANCHOR_CLI} anchor-cli --locked

# Build a dummy program to bootstrap the BPF SDK (doing this speeds up builds).
RUN mkdir -p /tmp && cd tmp && anchor init dummy && cd dummy && anchor build

WORKDIR /workdir
