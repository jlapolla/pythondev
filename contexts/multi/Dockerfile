FROM ubuntu@sha256:29934af957c53004d7fb6340139880d23fb1952505a15d69a03af0d1418878cb

###
### Setup apt
###

# Need apt-utils and software-properties-common to use add-apt-repository
# to add PPA repositories.

RUN     DEBIAN_FRONTEND=noninteractive apt-get update -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get install -yq apt-utils \
     && DEBIAN_FRONTEND=noninteractive apt-get install -yq software-properties-common \
     && DEBIAN_FRONTEND=noninteractive apt-get autoremove -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get autoclean -yq \
     && rm -rf /var/lib/apt/lists/*

###
### Enable man pages
###

RUN     rm /etc/dpkg/dpkg.cfg.d/excludes \
     && DEBIAN_FRONTEND=noninteractive apt-get update -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get install -yq man \
                                                           less \
     && DEBIAN_FRONTEND=noninteractive apt-get autoremove -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get autoclean -yq \
     && rm -rf /var/lib/apt/lists/*

###
### Add developer tools
###

RUN     DEBIAN_FRONTEND=noninteractive apt-get update -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get install -yq git \
                                                           ssh-client \
                                                           vim \
     && DEBIAN_FRONTEND=noninteractive apt-get autoremove -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get autoclean -yq \
     && rm -rf /var/lib/apt/lists/*

###
### Add deadsnakes PPA
###

RUN     DEBIAN_FRONTEND=noninteractive add-apt-repository ppa:deadsnakes/ppa \
     && DEBIAN_FRONTEND=noninteractive apt-get autoremove -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get autoclean -yq \
     && rm -rf /var/lib/apt/lists/*

###
### Add pythons
###

RUN     DEBIAN_FRONTEND=noninteractive apt-get update -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get install -yq python3.7 \
                                                           python3.7-dev \
                                                           python3.6 \
                                                           python3.6-dev \
                                                           python3.5 \
                                                           python3.5-dev \
                                                           python3.4 \
                                                           python3.4-dev \
                                                           python3.3 \
                                                           python3.3-dev \
                                                           python2.7 \
                                                           python2.7-dev \
                                                           python2.6 \
                                                           python2.6-dev \
     && DEBIAN_FRONTEND=noninteractive apt-get autoremove -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get autoclean -yq \
     && rm -rf /var/lib/apt/lists/*

###
### Add virtual environment for tox
###

# Create python virtual environment for latest version of python, and
# install tox. We don't need virtual environments for other pythons we
# wish to test because tox will create the virtual environments for us.
#
# FYI: Last setuptools that works with python2.6: setuptools==36.8.0

RUN     DEBIAN_FRONTEND=noninteractive apt-get update -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get install -yq python3-venv \
                                                           python3.7-venv \
     && DEBIAN_FRONTEND=noninteractive apt-get autoremove -yq \
     && DEBIAN_FRONTEND=noninteractive apt-get autoclean -yq \
     && rm -rf /var/lib/apt/lists/* \
     && python3.7 -m venv --system-site-packages /venv/py37 \
     && . /venv/py37/bin/activate \
     && pip install --no-cache -U pip \
     && pip install --no-cache tox

###
### Create user
###

# To ensure your container user has the same uid as your host user, invoke
# `docker build` as:
#
# docker build --build-arg uid=$(id -u)
#
# This ensures that any files and directories you create in bind mounts
# within the container will have the appropriate permissions on the host.

ARG uid=1000
RUN adduser --uid ${uid} --disabled-password --gecos "" user
USER user:user
WORKDIR /home/user

###
### Personalization
###

# Vim plugins.
COPY --chown=user:user .vimrc /home/user/
RUN     git clone https://github.com/VundleVim/Vundle.vim.git .vim/bundle/Vundle.vim \
     && vim -c PluginInstall -c qall 1>/dev/null 2>&1

# All other dotfiles. These are copied after installing Vim plugins, so that a
# change to one of these dotfiles does not force the Vim plugins to be
# reinstalled.
COPY --chown=user:user .bashrc_custom .gitconfig .gitignore_global .inputrc \
     /home/user/
# Have to copy .config separately since it is a directory.
COPY --chown=user:user .config /home/user/.config
RUN     printf "\n%s\n" "test -s ~/.bashrc_custom && source ~/.bashrc_custom" 1>>.bashrc
