FROM debian:buster-slim

# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        \
        # .NET Core dependencies
        libc6 \
        libgcc1 \
        libgssapi-krb5-2 \
        libicu63 \
        libssl1.1 \
        libstdc++6 \
        zlib1g \
        curl \
        git \
        procps \
        wget \
    #
    # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support for the non-root user
    # && apt-get install -y sudo \
    # && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
    # && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*


ENV \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    DOTNET_INSTALL_DIR=/usr/share/dotnet/ \
    DOTNET_ROOT=/usr/share/dotnet/

COPY ./.devcontainer/install-dotnets.sh global.json* .

RUN /bin/bash install-dotnets.sh

ENV PATH="$DOTNET_ROOT:${PATH}"
RUN ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet

RUN  dotnet --info

# Copy endpoint specific user settings into container to specify
# .NET Core should be used as the runtime.
COPY ./.devcontainer/settings.vscode.json /root/.vscode-remote/data/Machine/settings.json

USER vscode
ENV HOME /home/vscode