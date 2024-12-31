FROM debian:stretch-slim AS build

# Update stretch repositories
RUN sed -i -e 's/deb.debian.org/archive.debian.org/g' \
           -e 's|security.debian.org|archive.debian.org/|g' \
           -e '/stretch-updates/d' /etc/apt/sources.list

# Add MIPS architecture and install build dependencies
RUN dpkg --add-architecture mips && \
        apt-get update && \
        apt-get -y install \
        build-essential \
        dpkg-cross \
        g++-mips-linux-gnu \
        git \
        bison \
        flex \
        wget && \
        rm -rf /var/lib/apt/lists/*

# BIRD args
ENV CROSSCOMPILE="mips-linux-gnu"
ARG BIRD_VERSION

# Configure BIRD
RUN wget https://bird.network.cz/download/bird-$BIRD_VERSION.tar.gz && \
        tar xzfv bird-$BIRD_VERSION.tar.gz && \
        cd bird-$BIRD_VERSION && \
        ./configure --host=mips-linux-gnu --prefix=/config/custom/bird --sysconfdir=/config/custom/bird --runstatedir=/run --disable-client

# Compile BIRD
RUN cd bird-$BIRD_VERSION && \
	make

FROM scratch AS export

ARG BIRD_VERSION
WORKDIR /

COPY --from=build /bird-$BIRD_VERSION/bird .
COPY --from=build /bird-$BIRD_VERSION/birdcl .
