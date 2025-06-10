FROM --platform=linux/amd64 node:lts-bookworm-slim
SHELL ["/bin/bash", "-c"]

# Create a non-root user
RUN useradd -m -s /bin/bash ubuntu

ENV NVM_DIR=/home/ubuntu/.nvm
ENV NODE_VERSION=22.15.0

# Install dependencies and clean up in one layer
RUN apt update && apt install -y \
    curl \
    bash \
    git \
    tar \
    gzip \
    libc++-dev \
    jq \
    python3 \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Create NVM directory and set permissions
RUN mkdir -p $NVM_DIR && chown -R ubuntu:ubuntu $NVM_DIR

# Install nvm, node and npm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash \
    && . "$NVM_DIR/nvm.sh" \
    && nvm install ${NODE_VERSION} \
    && nvm use ${NODE_VERSION} \
    && nvm alias default ${NODE_VERSION}

# Add node and npm to path
ENV PATH="${NVM_DIR}/versions/node/v${NODE_VERSION}/bin:${PATH}"
RUN npm i -g yarn

# Set working directory and user
WORKDIR /home/ubuntu
USER ubuntu

# Add healthcheck
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:3000/ || exit 1
