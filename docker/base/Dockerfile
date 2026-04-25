FROM --platform=linux/arm64 ubuntu:24.04

ARG NON_ROOT_USER=ccuser
ARG TZ=Asia/Tokyo

# Set timezone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Set locale
RUN apt-get update && \
    apt-get install -y --no-install-recommends locales && \
    locale-gen ja_JP.UTF-8 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
ENV LANG=ja_JP.UTF-8
ENV LC_ALL=ja_JP.UTF-8

# Install basic development tools and iptables/ipset
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    git \
    less \
    procps \
    unzip \
    gnupg2 \
    gh \
    dnsutils \
    jq \
    vim \
    wget \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN useradd -u 501 -m ${NON_ROOT_USER}

RUN update-ca-certificates

# Ensure default node user has access to /usr/local/share
RUN mkdir -p /usr/local/share/npm-global && \
    chown -R ${NON_ROOT_USER}:${NON_ROOT_USER} /usr/local/share

ARG WORKING_DIR=/workspace
WORKDIR ${WORKING_DIR}

# Create workspace and config directories and set permissions
RUN mkdir -p ${WORKING_DIR} && \
    chown -R ${NON_ROOT_USER}:${NON_ROOT_USER} ${WORKING_DIR}

## uv
RUN curl -LsSf https://astral.sh/uv/install.sh | UV_INSTALL_DIR=/usr/local/bin sh

## Python tools
ENV UV_TOOL_BIN_DIR=/usr/local/bin
RUN uv tool install ruff
RUN uv tool install pytest

## go
ARG GO_VERSION=1.26.2
RUN wget https://go.dev/dl/go${GO_VERSION}.linux-arm64.tar.gz && \
    tar -C /usr/local -xzf go${GO_VERSION}.linux-arm64.tar.gz && \
    rm -rf go${GO_VERSION}.linux-arm64*
ENV PATH="/usr/local/go/bin:${PATH}"

## gopls
RUN GOBIN=/usr/local/bin go install golang.org/x/tools/gopls@latest

## pylsp
RUN apt-get update && \
    apt-get install -y --no-install-recommends python3-pylsp && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

## terraform-ls
RUN apt-get install -y --no-install-recommends gpg && \
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com noble main" | tee /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends terraform-ls && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Set up non-root user
USER ${NON_ROOT_USER}

# Install global packages
ENV NPM_CONFIG_PREFIX=/usr/local/share/npm-global
ENV PATH="/usr/local/share/npm-global/bin:${PATH}"

RUN uv python install 3.13 && echo 'alias python="$(uv python find 3.13)"' >> ~/.bashrc
