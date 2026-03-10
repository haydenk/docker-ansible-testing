ARG VERSION=9-minimal

FROM --platform=$TARGETPLATFORM rockylinux:${VERSION}

ARG VERSION
LABEL maintainer="Hayden King" distro=rockylinux version=${VERSION}

ENV pip_packages="ansible"

# hadolint ignore=DL3041
RUN microdnf -y install rpm dnf dnf-plugins-core \
 && microdnf clean all \
 && dnf -y update \
 && dnf -y install \
      epel-release \
      initscripts \
      sudo \
      which \
      hostname \
      libyaml \
      python3 \
      python3-pip \
      python3-pyyaml \
      iproute \
      gnupg2 \
 && (dnf -y install python39 python39-pip || true) \
 && dnf clean all \
 && rm -f /lib/systemd/system/multi-user.target.wants/* \
          /etc/systemd/system/*.wants/* \
          /lib/systemd/system/local-fs.target.wants/* \
          /lib/systemd/system/sockets.target.wants/*udev* \
          /lib/systemd/system/sockets.target.wants/*initctl* \
          /lib/systemd/system/basic.target.wants/* \
          /lib/systemd/system/anaconda.target.wants/*

# hadolint ignore=DL3013
RUN python3.9 -m pip install --no-cache-dir --upgrade pip \
 && python3.9 -m pip install --no-cache-dir $pip_packages \
 && sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/' /etc/sudoers \
 && mkdir -p /etc/ansible \
 && printf '[local]\nlocalhost ansible_connection=local\n' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup"]
CMD ["/usr/lib/systemd/systemd"]
