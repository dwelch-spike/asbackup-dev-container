FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

# Install build and runtime dependencies for asbackup/asrestore
USER root
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
		git \
		build-essential \
		pkg-config \
		cmake \
		zlib1g-dev \
		libssl-dev \
		libuv1-dev \
		libcurl4-openssl-dev \
		libzstd-dev \
		libjansson-dev \
		ca-certificates \
		curl \
		gdb \
	&& rm -rf /var/lib/apt/lists/*

# Build and install aws-sdk-cpp v1.10.55 (S3 only, shared libs)
ARG AWS_SDK_CPP_VERSION=1.10.55
RUN set -eux; \
	mkdir -p /opt && cd /opt; \
	git clone https://github.com/aws/aws-sdk-cpp.git; \
	cd aws-sdk-cpp; \
	git checkout tags/${AWS_SDK_CPP_VERSION}; \
	git submodule update --init --recursive; \
	cmake -S . -B build \
		-DCMAKE_BUILD_TYPE=Release \
		-DBUILD_ONLY="s3" \
		-DBUILD_SHARED_LIBS=ON \
		-DENABLE_TESTING=OFF \
		-DCMAKE_INSTALL_PREFIX=/usr/local \
		-DCMAKE_INSTALL_LIBDIR=lib; \
	cmake --build build -j"$(nproc)"; \
	cmake --install build; \
	rm -rf /opt/aws-sdk-cpp

# Ensure dynamic linker can find /usr/local/lib at runtime
RUN echo '/usr/local/lib' > /etc/ld.so.conf.d/usr-local-lib.conf && ldconfig

USER vscode

