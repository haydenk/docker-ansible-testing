ARG VERSION=noble

FROM --platform=$TARGETPLATFORM ubuntu:${VERSION}

ARG VERSION
LABEL maintainer="Hayden King" distro=ubuntu version=${VERSION}

ARG DEBIAN_FRONTEND=noninteractive

ENV pip_packages="ansible"

# hadolint ignore=DL3008,SC2016
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       apt-utils \
       build-essential \
       locales \
       libffi-dev \
       libssl-dev \
       libyaml-dev \
       python3-dev \
       python3-setuptools \
       python3-pip \
       python3-yaml \
       software-properties-common \
       rsyslog systemd systemd-cron sudo iproute2 \
    && sed -i 's/^\($ModLoad imklog\)/#\1/' /etc/rsyslog.conf \
    && locale-gen en_US.UTF-8 \
    && find /usr/lib/python3* -name EXTERNALLY-MANAGED -delete 2>/dev/null || true \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /usr/share/doc /usr/share/man

# hadolint ignore=DL3013
RUN pip3 install --no-cache-dir $pip_packages

RUN mkdir -p /etc/ansible \
 && printf '[local]\nlocalhost ansible_connection=local\n' > /etc/ansible/hosts \
 && rm -f /lib/systemd/system/systemd*udev* \
 && rm -f /lib/systemd/system/getty.target

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/lib/systemd/systemd"]
