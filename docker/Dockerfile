FROM ubuntu:22.04

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        git \
        dnsutils \
        locales \
        ldap-utils \
        libnss-ldapd \
        libpam-ldapd \
        less \
        nano \
        nodejs \
        nscd \
        nslcd \
        python-is-python3 \
        python3 \
        python3-pip \
        rsync \
        unzip \
        vim \
        wget \
        zip \
    && rm -rf /var/lib/apt/lists/*  \
    && localedef -i en_NZ -c -f UTF-8 -A /usr/share/locale/locale.alias en_NZ.UTF-8

ENV LANG en_NZ.utf8

# install kubectl, required for running on the k8s cluster
ARG KUBECTL_VERSION=v1.28.5
RUN curl -LO https://dl.k8s.io/release/$KUBECTL_VERSION/bin/linux/amd64/kubectl \
    && mv kubectl /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# copy in ood k8s utils
ARG UTILS_HASH=6298fb01f7a7c66a8454e3f0fd74437a32491423
RUN git clone https://github.com/nesi/training-environment-k8s-utils.git /opt/ood-k8s-utils \
    && cd /opt/ood-k8s-utils \
    && git checkout $UTILS_HASH \
    && chmod +x /opt/ood-k8s-utils/files/* \
    && mv /opt/ood-k8s-utils/files/* /bin/ \
    && rm -rf /opt/ood-k8s-utils


# datasets pre-downloaded by CI into docker/data/ and copied here
COPY data/flower_photos /var/lib/flower_photos
COPY data/tensorflow_datasets /var/lib/tensorflow_datasets
RUN chown -R root:root /var/lib/flower_photos /var/lib/tensorflow_datasets \
    && chmod -R o+rX /var/lib/flower_photos /var/lib/tensorflow_datasets

ENV TFDS_DATA_DIR=/var/lib/tensorflow_datasets

# copy the repo source (e.g. notebooks) to the container image and install dependencies
ARG ML102_VERSION="v0.1.3"

RUN git clone --depth 1 --branch $ML102_VERSION https://github.com/nesi/ml102_workshop.git /opt/ml102_workshop \
    && cd /opt/ml102_workshop \
    && pip3 --no-cache-dir install -r requirements.txt \
    && pip3 --no-cache-dir install jupyterlab \
    && rm -rf /opt/ml102_workshop/.git
