ARG BASE_IMAGE
ARG CLAUDE_CODE_VERSION=latest
ARG NON_ROOT_USER=ccuser

FROM --platform=linux/arm64 ${BASE_IMAGE}

USER ${NON_ROOT_USER}

ENV SHELL=/bin/bash
ENV EDITOR=vim
ENV VISUAL=vim

RUN curl -fsSL https://claude.ai/install.sh | bash
ENV PATH="/home/${NON_ROOT_USER}/.local/bin:${PATH}"
