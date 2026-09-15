ARG DEBIAN_IMAGE=debian:trixie-slim@sha256:d7e12182ce18b85b93007c1dedf31f2d29e01ccf3182cc4017c709b6259bc132
FROM ${DEBIAN_IMAGE} AS dev

ARG NODE_VERSION=24.21.0

# VARIANT can be either 'hugo' for the standard version or 'hugo_extended' for the extended version.
ARG HUGO_VARIANT=hugo
# VERSION can be either 'latest' or a specific version number
ARG HUGO_VERSION=0.165.0

# Install the runtime and explicitly retained development tools. Avoid the
# compiler-heavy toolchain inherited from the JavaScript devcontainer image.
RUN apt-get update && apt-get install --no-install-recommends -y \
    ca-certificates \
    curl \
    git \
    gnupg \
    make \
    sudo \
    wget \
    xz-utils \
    zsh \
    stow \
    zsh-antigen \
    inotify-tools \
    rclone \
    ffmpeg \
    pup \
    jq \
    gh \
    tidy \
    ripgrep \
    && rm -rf /var/lib/apt/lists/*

# Match the standard devcontainer user so the bind-mounted workspace remains writable.
RUN groupadd --gid 1000 node \
    && useradd --uid 1000 --gid node --create-home --shell /bin/zsh node \
    && echo 'node ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/node \
    && chmod 0440 /etc/sudoers.d/node

# Install the pinned Node release without inheriting buildpack-deps and its toolchain.
RUN set -eux; \
    case "$(dpkg --print-architecture)" in \
        amd64) node_arch='x64' ;; \
        arm64) node_arch='arm64' ;; \
        *) echo "Unsupported Node architecture: $(dpkg --print-architecture)" >&2; exit 1 ;; \
    esac; \
    curl -fsSLO "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-${node_arch}.tar.xz"; \
    curl -fsSLO "https://nodejs.org/dist/v${NODE_VERSION}/SHASUMS256.txt"; \
    grep " node-v${NODE_VERSION}-linux-${node_arch}.tar.xz$" SHASUMS256.txt | sha256sum -c -; \
    tar -xJf "node-v${NODE_VERSION}-linux-${node_arch}.tar.xz" -C /usr/local --strip-components=1 --no-same-owner; \
    rm "node-v${NODE_VERSION}-linux-${node_arch}.tar.xz" SHASUMS256.txt; \
    node --version; \
    npm --version

# Download Hugo.
RUN set -eux; \
    case "$(dpkg --print-architecture)" in \
        amd64) hugo_arch='amd64' ;; \
        arm64) hugo_arch='arm64' ;; \
        *) echo "Unsupported Hugo architecture: $(dpkg --print-architecture)" >&2; exit 1 ;; \
    esac; \
    wget -q "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/${HUGO_VARIANT}_${HUGO_VERSION}_linux-${hugo_arch}.deb"; \
    dpkg -i "${HUGO_VARIANT}_${HUGO_VERSION}_linux-${hugo_arch}.deb"; \
    rm "${HUGO_VARIANT}_${HUGO_VERSION}_linux-${hugo_arch}.deb"

# Install starship prompt
RUN sh -c "$(curl -fsSL https://starship.rs/install.sh)" -- --bin-dir /usr/local/bin -y

# ZSH storage
RUN mkdir -p /home/node/.zsh /workspace \
    && chown -R node:node /home/node/.zsh /workspace
VOLUME [ "/home/node/.zsh" ]
WORKDIR /workspace

# Hugo dev server port
EXPOSE 1313

USER node

# Install opencode
RUN curl -fsSL https://opencode.ai/install | bash
