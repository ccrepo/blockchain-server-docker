# Dockerfile

# Use the official Ubuntu 22.04 base image
FROM ubuntu:22.04

# Environment
ARG SERVER_DOCKER_GITUSER=default_value

ARG SERVER_DOCKER_BITCOIN_VERSION=default_value

ARG FULL_NODE=default_value

ARG BITCOIN_GIT_DEVUSER=default_value

# Info
RUN bash -c "echo starting using github user SERVER_DOCKER_GITUSER = '${SERVER_DOCKER_GITUSER}', \
 SERVER_DOCKER_BITCOIN_VERSION = '${SERVER_DOCKER_BITCOIN_VERSION}', BITCOIN_GIT_DEVUSER = '${BITCOIN_GIT_DEVUSER}' \
 and FULL_NODE = '${FULL_NODE}' "

# Install general
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y apt-utils default-jdk \
    git gradle curl wget openssl net-tools iputils-ping traceroute autoconf pkg-config libtool emacs \
    build-essential openssh-server openssh-client tomcat9 && \
    rm -rf /var/lib/apt/lists/*

# Install bitcoin
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y automake autotools-dev bsdmainutils build-essential \
    ccache clang gcc git libboost-dev libboost-filesystem-dev libboost-system-dev libboost-test-dev libevent-dev \
    libminiupnpc-dev libnatpmp-dev libsqlite3-dev libtool libzmq3-dev pkg-config python3 qtbase5-dev qttools5-dev \
    qttools5-dev-tools qtwayland5 systemtap-sdt-dev && \
    rm -rf /var/lib/apt/lists/*

# Install desktop
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y plasma-desktop xrdp && \
    rm -rf /var/lib/apt/lists/*

RUN echo '' >> ~/.bashrc
RUN echo 'LS_COLORS="di=1;93"' >> ~/.bashrc

WORKDIR /usr/local/src

ARG CACHEBUST

RUN echo "CACHEBUST is '$CACHEBUST'"

RUN git clone https://github.com/${SERVER_DOCKER_GITUSER}/blockchain-bitcoin.git

WORKDIR /usr/local/src/blockchain-bitcoin/bin

RUN ls -l

RUN chmod 700 ./c

RUN ./c -GITUSER "${SERVER_DOCKER_GITUSER}" -BITCOIN_GITUSER "${BITCOIN_GIT_DEVUSER}" \
 -FULL_NODE "${FULL_NODE}" -BITCOIN_VERSION "${SERVER_DOCKER_BITCOIN_VERSION}"
 
# Set the working directory
# WORKDIR /usr/local/src

# Expose the default Tomcat port
ENV CATALINA_BASE /var/lib/tomcat9
ENV CATALINA_HOME /usr/share/tomcat9
ENV PATH $CATALINA_HOME/bin:$PATH

# Create data dir for volume data
RUN mkdir /data 2>/dev/null

# Expose ports
EXPOSE 22
EXPOSE 8080
EXPOSE 18444

# # Start when the container starts
ENTRYPOINT ( catalina.sh start ) && ( service ssh start ) && ( tail -f /dev/null )
