# Start from an Ubuntu base image
FROM ubuntu:20.04

# Disable prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Set up working directory
WORKDIR /inspector

# Install packages
RUN apt-get update && \
    apt-get install -y git curl wget jq build-essential uuid-runtime && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Define environment variables for Rust installation
ENV CARGO_HOME=/usr/local/cargo
ENV RUSTUP_HOME=/usr/local/rustup
ENV PATH="${PATH}:/usr/local/cargo/bin"

# Install Rust using rustup (into /usr/local/cargo for global access)
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --no-modify-path --profile minimal \
    && chmod -R 777 /usr/local/cargo /usr/local/rustup

# Define the version argument for rustc with a default value
ARG RUST_VERSION=""

# Install the specified version of rustc
RUN if [ "$RUST_VERSION" ]; then \
         rustup install "$RUST_VERSION"; \
         rustup default "$RUST_VERSION"; \
       fi

# Install the wasm32-unknown-unknown target
RUN rustup target add wasm32-unknown-unknown

# Define the version argument for stellar-cli with a default value
ARG STELLAR_CLI_VERSION=""

# Install stellar-cli 
RUN if [ -z "$STELLAR_CLI_VERSION" ]; then \
         cargo install --locked stellar-cli --features opt; \
       else \
         cargo install --locked stellar-cli --features opt --version $STELLAR_CLI_VERSION; \
       fi

# Print versions to verify installation
RUN rustc --version && \
    cargo --version && \
    stellar --version

# Specify the package to build
ENV PACKAGE=${PACKAGE}

# Specify the make target
ENV MAKE_TARGET=${MAKE_TARGET}

# Specify the working directory
ENV RELATIVE_PATH=${RELATIVE_PATH}

# Specify the repo
ENV REPO=${REPO}

# Specify home domain
ENV HOME_DOMAIN=${HOME_DOMAIN}

# Copy entrypoint script
COPY /entrypoint.sh /usr/local/bin/

# Make the entrypoint script executable
RUN chmod +x /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]