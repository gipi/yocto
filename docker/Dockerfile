# Dockefile for building yocto using a supported distribution
#
# It's supposed to be run mounting the directory in /app
FROM ubuntu:18.04

# install dependencies for Yocto
RUN apt update && apt -y install gawk \
    wget \
    git-core \
    diffstat \
    unzip \
    texinfo \
    gcc-multilib \
     build-essential \
     chrpath \
     socat \
     libsdl1.2-dev \
     cpio \
     locales \
     python \
     python-pip \
     python3-pip

# set a locale otherwise bitbake goes banana
RUN locale-gen en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8

# install kas
RUN pip3 install kas

# this is the working directory
WORKDIR /app

CMD kas build kas-bbb-backups.yml core-image-full-cmdline
