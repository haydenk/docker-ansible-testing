ARG VERSION=bookworm-slim

FROM --platform=$TARGETPLATFORM debian:${VERSION}

ARG VERSION
LABEL maintainer="Hayden King" distro=debian version=${VERSION}

ARG DEBIAN_FRONTEND=noninteractive

ENV pip_packages="ansible cryptography"

# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       sudo systemd systemd-sysv \
       build-essential wget libffi-dev libssl-dev procps \
       python3-pip python3-dev python3-setuptools python3-wheel python3-apt \
       iproute2 dbus \
    && find /usr/lib/python3* -name EXTERNALLY-MANAGED -delete 2>/dev/null || true \
    && rm -rf /var/lib/apt/lists/* /usr/share/doc /usr/share/man \
    && apt-get clean

# hadolint ignore=DL3013
RUN pip3 install --no-cache-dir $pip_packages

RUN mkdir -p /etc/ansible \
 && printf '[local]\nlocalhost ansible_connection=local\n' > /etc/ansible/hosts \
 && rm -f /lib/systemd/system/multi-user.target.wants/getty.target

VOLUME ["/sys/fs/cgroup"]
CMD ["/lib/systemd/systemd"]
