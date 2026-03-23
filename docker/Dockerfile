# To build the docker image:
# docker build --platform=linux/amd64 -t devenvgo/ubuntu:0.1 ./docker/
#
# To create the docker container and open the terminal
# This will create a volume for us to persist our files. We can mount this volume in other containers later if we like.
#   The volume differs from mounting a directory from the host OS, in that docker will manage it and we won't access it from the host. 
#   That way we don't have to worry about line endings and such.
# docker run --name devenvgo --user developer -it --mount type=volume,src=vol_devenvgo,dst=/home/developer/volume devenvgo/ubuntu:0.1 bash
# 
# When done, just 'exit'
# 
# To start the already created docker container and open the terminal
# docker start --attach --interactive devenvgo
#
# When done, just 'exit'

FROM ubuntu:24.04
SHELL ["/bin/bash", "-c"]
LABEL Description="Matthew's Ubuntu based development environment for golang"

ARG USERNAME=developer
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG GO_VERSION=1.26.0

# Set environment variables
#ENV GOPATH /go
#ENV PATH $GOPATH/bin:$PATH
ENV PATH=$PATH:/usr/local/go/bin

# Install all the things we'll need
#    I am installing git on the container rather than the host, because we are using the volume rather than mounting a directory we share with the host
#    in my opinion, doing the latter invites all kinds of problems.
RUN apt-get update && apt-get -y --no-install-recommends install \
    sudo \
    bash \
    vim \
    git \
    ssh \
    build-essential \
    wget \
    ca-certificates \
    curl \
    python3 \
    python3-pip

# Download and extract the specified Go version
RUN curl -fsSL "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz" | tar -C /usr/local -xz

# Verify the installation
RUN go version

# Install gopls
RUN go clean -modcache && go install -v golang.org/x/tools/gopls@latest

# Verify installation
#RUN gopls version

# Create a non-root user named 'developer' with a home directory and bash as default shell
RUN useradd -ms /bin/bash ${USERNAME}

# Add the 'developer' to the 'sudo' group
RUN usermod -aG sudo ${USERNAME}

# Configure the 'sudo' group for NOPASSWD access (prevents TTY errors in Docker)
# This allows 'developer' to use 'sudo' without a password prompt.
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Change to the user
USER ${USERNAME}
WORKDIR /home/${USERNAME}

# As the user, make the directory we will map the volume to, as the user, so it has proper permissions
RUN mkdir -p /home/${USERNAME}/volume
