FROM ubuntu:22.04

WORKDIR /home/

COPY . .

RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install -y \
  curl \
  git \
  gnupg2 \
  jq \
  sudo \
  zsh \
  vim \
  build-essential \
  openssl

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
RUN /root/.cargo/bin/rustup install nightly
RUN /root/.cargo/bin/rustup component add rustfmt
RUN /root/.cargo/bin/rustup component add rustfmt --toolchain nightly
RUN /root/.cargo/bin/rustup component add clippy 
RUN /root/.cargo/bin/rustup component add clippy --toolchain nightly

ENV PATH="/root/.cargo/bin:$PATH"
