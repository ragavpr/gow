# syntax=docker/dockerfile:1.4
ARG BASE_APP_IMAGE 

# hadolint ignore=DL3006
FROM ${BASE_APP_IMAGE}

# 
# Prevent firefox snap
COPY scripts/ff-unsnap /etc/apt/preferences.d/ff-unsnap

# \
# Setup Firefox PPA \
RUN apt-get update && \
    apt-get install -y --no-install-recommends software-properties-common gpg-agent && \
    add-apt-repository -y ppa:mozillateam/ppa && \
    apt-get update

# \
# Install core packages \
ARG CORE_PACKAGES=" \
    lsb-release \
    wget \
    gnupg2 \
    dbus-x11 \
    firefox \
    flatpak \
    sudo \
    "

RUN apt-get install -y $CORE_PACKAGES

# \
# Install de \
ARG DE_PACKAGES=" \
    xfce4 \
    xfce4-settings \
    xubuntu-default-settings \
    xubuntu-icon-theme \
    papirus-icon-theme arc-theme \
    at-spi2-core \
    "

RUN apt-get install -y $DE_PACKAGES

# \
# Install additional apps \
ARG ADDITIONAL_PACKAGES=" \
    xfce4-terminal \
    xfce4-taskmanager \
    xfce4-whiskermenu-plugin \
    xfce4-docklike-plugin \
    xarchiver \
    mousepad \
    zip unzip p7zip-full \
    gnome-software gnome-software-plugin-flatpak \
    "

RUN apt-get install -y --no-install-recommends $ADDITIONAL_PACKAGES

ARG DEV_PACKAGES=" \
    git \
    curl \
    vim \
    nano \
    htop \
    ark \
    xdg_utils \
    net-tools \
    iputils-ping \
    jq \
    less \
    "

RUN apt-get install -y --no-install-recommends $DEV_PACKAGES

RUN \
    # \
    # Fixes \
    rm -f \
    /etc/xdg/autostart/xscreensaver.desktop && \
    # \
    # Clean \
    apt update && \
    apt-get remove -y foot && \
    apt autoremove -y &&\
    apt clean && \
    rm -rf \
    /config/.cache \
    /var/lib/apt/lists/* \
    /var/tmp/* \
    /tmp/*

# 
# Replace launch scripts
COPY --chmod=777 scripts/launch-comp.sh scripts/startup.sh /opt/gow/
COPY --chmod=777 scripts/startdbus.sh /opt/gow/startdbus

#
# Include default xfce config
COPY --chmod=777 --chown=retro:retro scripts/xfce4 /opt/gow/xfce4

# 
# Fix locals
COPY scripts/locale /etc/default/locale

# 
# Allow anyone to start dbus without password
RUN echo "\nALL ALL=NOPASSWD: /opt/gow/startdbus" >> /etc/sudoers

# 
# Fix bwarp perms for flatpaks
RUN chmod u+s /usr/bin/bwrap

ENV XDG_RUNTIME_DIR=/tmp/.X11-unix

ARG IMAGE_SOURCE
LABEL org.opencontainers.image.source=$IMAGE_SOURCE
