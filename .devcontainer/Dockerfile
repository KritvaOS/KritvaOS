# ============================================================================
# Kritva Development Container
# ============================================================================
#
# Purpose:
#   Canonical development environment for Kritva v0.1.
#
# Source of truth:
#   toolchain/VERSIONS.yaml
#
# Principle:
#   Host machines provide Git + Docker.
#   Development tools are provided by this container.
#
# ============================================================================

FROM ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive

# ----------------------------------------------------------------------------
# Basic environment
# ----------------------------------------------------------------------------

ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

ENV DEBIAN_FRONTEND=noninteractive

# Prevent interactive prompts during package installation.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        wget \
        git \
        sudo \
        bash \
        vim \
        nano \
        less \
        file \
        tree \
        unzip \
        zip \
        pkg-config \
        build-essential \
        cmake \
        ninja-build \
        clang \
        clang-format \
        clang-tidy \
        lldb \
        gdb \
        python3 \
        python3-pip \
        python3-venv \
        python3-yaml \
        shellcheck \
        && \
    rm -rf /var/lib/apt/lists/*


# ----------------------------------------------------------------------------
# Kritva developer user
# ----------------------------------------------------------------------------

ARG USERNAME=kritvaos

RUN if id -u ${USERNAME} >/dev/null 2>&1; then \
        echo "User ${USERNAME} already exists"; \
    else \
        groupadd ${USERNAME} && \
        useradd \
            --gid ${USERNAME} \
            --create-home \
            --shell /bin/bash \
            ${USERNAME}; \
    fi && \
    echo "${USERNAME} ALL=(root) NOPASSWD:ALL" \
        > /etc/sudoers.d/${USERNAME} && \
    chmod 0440 /etc/sudoers.d/${USERNAME}

# ----------------------------------------------------------------------------
# Python environment
# ----------------------------------------------------------------------------

RUN python3 --version && \
    python3 -m pip --version || true

# PyYAML is required by scripts/check_toolchain.py.
#
# Ubuntu's python3-yaml package is installed above to keep the base image
# compatible with Ubuntu's externally-managed Python environment.
#
# Additional Python dependencies should be introduced through a dedicated
# Kritva requirements/lock file when required.
RUN python3 -c "import yaml; print('PyYAML:', yaml.__version__)"

# ----------------------------------------------------------------------------
# Workspace
# ----------------------------------------------------------------------------

WORKDIR /workspaces/KritvaOS

RUN chown -R ${USERNAME}:${USERNAME} /workspaces

USER ${USERNAME}

# ----------------------------------------------------------------------------
# Shell defaults
# ----------------------------------------------------------------------------

RUN echo 'export TERM=${TERM:-xterm-256color}' >> /home/${USERNAME}/.bashrc && \
    echo 'cd /workspaces/KritvaOS' >> /home/${USERNAME}/.bashrc

# ----------------------------------------------------------------------------
# Default command
# ----------------------------------------------------------------------------

CMD ["/bin/bash"]
