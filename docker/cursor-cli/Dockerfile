ARG BASE_IMAGE
FROM ${BASE_IMAGE}

ARG WORKING_DIR=/workspace
ARG NON_ROOT_USER=ccuser

WORKDIR ${WORKING_DIR}
USER ${NON_ROOT_USER}

ENV SHELL=/bin/bash
ENV EDITOR=vim
ENV VISUAL=vim
ENV PATH="/home/${NON_ROOT_USER}/.local/bin:${PATH}"

ARG CACHE_BUST
RUN curl https://cursor.com/install -fsS | bash
