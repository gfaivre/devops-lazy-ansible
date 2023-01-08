ARG DEBIAN=bullseye

########
# Base #
########

FROM debian:${DEBIAN}-slim as base

ARG DEBIAN
ARG USER_ID=1000
ARG GROUP_ID=1000
ARG GOSU_VERSION=1.16
ARG GOMPLATE_VERSION=3.11.3

# The 'container' environment variable tells systemd that it's running inside a
# Docker container environment.
# It's also internally used for checking we're running inside a container.
ENV container="docker"

RUN \
    # Backports
    printf "deb http://deb.debian.org/debian ${DEBIAN}-backports main" > /etc/apt/sources.list.d/backports.list \
    && apt-get update \
    && apt-get install --yes --no-install-recommends \
        s6 \
        sudo \
        curl \
        ca-certificates \
        gnupg \
        libarchive-tools bzip2 \
        bash-completion \
        rsync \
        git \
        make \
        less \
        vim \
        socat \
    # User
    && addgroup --gid ${GROUP_ID} lazy \
    && adduser --home /home/lazy --shell /bin/bash --uid ${USER_ID} --gecos lazy --ingroup lazy --disabled-password lazy \
    && mkdir -p /run/user/${USER_ID} && chown lazy:lazy /run/user/${USER_ID} \
    && printf "lazy ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/lazy \
    # Gosu
    && curl -sSL https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-$(case $(dpkg --print-architecture) in "amd64") echo "amd64" ;; "arm64") echo "arm64" ;; esac) \
        -o /usr/local/bin/gosu \
    && chmod +x /usr/local/bin/gosu \
    # Gomplate
    && curl -sSL https://github.com/hairyhenderson/gomplate/releases/download/v${GOMPLATE_VERSION}/gomplate_linux-$(case $(dpkg --print-architecture) in "amd64") echo "amd64" ;; "arm64") echo "arm64" ;; esac) \
        -o /usr/local/bin/gomplate \
    && chmod +x /usr/local/bin/gomplate \
    # Bash completion
    && mkdir -p /etc/bash_completion.d \
    # Oh My Bash
    && git clone https://github.com/ohmybash/oh-my-bash.git /usr/local/share/oh-my-bash

##########
# System #
##########

FROM base AS system

ARG DEBIAN

RUN \
    apt-get install --yes --no-install-recommends \
      openssh-client \
      sshpass \
      python3 python3-pip \
      python3-argcomplete \
      python3-apt \
      shellcheck \
    # Bash completion
    && activate-global-python-argcomplete3 --dest /etc/bash_completion.d

# Ansible
RUN \
    pip3 install ansible-core==2.14.1
