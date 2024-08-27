FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04 AS base

ARG PYTHON_VERSION=3.11
ARG VENV_IN_PROJECT=true
ARG TIMEZONE=Europe/Amsterdam

ENV GIT_PYTHON_REFRESH=quiet
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=$TIMEZONE
ENV HOME="/home/vscode"
ENV PYENV_ROOT="$HOME/.pyenv"
ENV PIPX_HOME="$HOME/.pipx"
ENV PIPX_DEFAULT_PYTHON="$HOME/.pyenv/shims/python"
ENV PRE_COMMIT_HOME="$HOME/workspace/.cache/pre-commit"

# Prerequisites for pyenv, poetry etc.
RUN apt-get update \
    && apt-get -y install libsqlite3-dev libffi-dev pipx gcc musl-dev python3-dev libbz2-dev openjdk-8-jdk \
    && rm -rf /var/lib/apt/lists/*

# Install pyenv
RUN curl https://pyenv.run | bash

ENV PATH="$PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH"
RUN echo 'eval "$(pyenv init -)"' >> /home/vscode/.bashrc \
    && pyenv install $PYTHON_VERSION \
    && pyenv global $PYTHON_VERSION \
    && pyenv rehash \
    && pip install --no-cache-dir --upgrade pip

# Poetry
RUN pipx install poetry

# pipx distros
ENV PATH="/home/vscode/.local/bin:$PATH"

# Versioning with poetry
RUN poetry self add poetry-git-version-plugin

# Create .venv in project
RUN poetry config virtualenvs.in-project ${VENV_IN_PROJECT}

RUN usermod -aG sudo vscode
RUN chmod -R 777 /home/vscode

FROM base AS just

RUN wget -qO - 'https://proget.makedeb.org/debian-feeds/prebuilt-mpr.pub' | gpg --dearmor | sudo tee /usr/share/keyrings/prebuilt-mpr-archive-keyring.gpg 1> /dev/null && \
    echo "deb [arch=all,$(dpkg --print-architecture) signed-by=/usr/share/keyrings/prebuilt-mpr-archive-keyring.gpg] https://proget.makedeb.org prebuilt-mpr $(lsb_release -cs)" | sudo tee /etc/apt/sources.list.d/prebuilt-mpr.list && \
    sudo apt-get update && \
    sudo apt-get -y install just && \
    rm -rf /var/lib/apt/lists/*

FROM just AS terraform

RUN apt-get update -y \
    && apt-get install -y gnupg software-properties-common \
    && wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list \
    && apt-get update \
    && apt-get install terraform \
    && rm -rf /var/lib/apt/lists/*

FROM terraform AS gcloud

RUN apt-get update -y \
    && apt-get install -y apt-transport-https \
        ca-certificates \
        gnupg \
        curl \
    && echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list \
    && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg \
    && apt-get update -y \
    && apt-get install google-cloud-sdk -y \
    && rm -rf /var/lib/apt/lists/*
