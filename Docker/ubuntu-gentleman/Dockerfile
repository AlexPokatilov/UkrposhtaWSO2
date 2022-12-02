FROM ubuntu:22.04

ENV USER=k8s
ENV PASS=k8s=admin!718
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && apt-get upgrade -y && apt-get install -y --no-install-recommends apt-utils && apt-get install sudo -y

RUN useradd ${USER} && echo "${USER}:${PASS}" | chpasswd && usermod -aG sudo ${USER}
WORKDIR /home/${USER}

RUN apt-get install -y \
mc \
nano \
net-tools \
dnsutils \
git \
tree \
htop \
iputils-ping \
wget \
curl \
unzip \
tar \
openssl \
curl \
ca-certificates
RUN apt-get clean -y
