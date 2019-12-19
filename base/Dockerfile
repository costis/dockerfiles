FROM ubuntu:bionic

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
	&& apt-get install -y \
	    curl \
	    netcat \
	    jq \
	    dnsutils \
	    tmux \
	    vim-nox \
	    iproute2 \
	&& rm -rf /var/lib/apt/lists/*

