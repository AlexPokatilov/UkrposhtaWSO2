FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y \
    && apt-get install -y --no-install-recommends apt-utils \
    && apt-get install -y sudo \
    mc \
    nano \
    net-tools \
    netcat \
    dnsutils \
    git \
    tree \
    htop \
    iputils-ping \
    wget \
    curl \
    unzip \
    zip \
    tar \
    openssl \
    curl \
    ca-certificates \
    tzdata \
    python-is-python3 \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*
