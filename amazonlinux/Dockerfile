ARG VERSION=2023

FROM --platform=$TARGETPLATFORM amazonlinux:${VERSION}

ARG VERSION
LABEL maintainer="Hayden King" distro=amazonlinux version=${VERSION}

ENV pip_packages="ansible"

# Install systemd -- See https://hub.docker.com/_/centos/
RUN yum -y install rpm dnf-plugins-core \
 && yum -y update \
 && yum -y install \
      initscripts \
      sudo \
      which \
      hostname \
      libyaml-devel \
      python3 \
      python3-pip \
      python3-pyyaml \
 && yum clean all \
 && rm -f /lib/systemd/system/multi-user.target.wants/* \
          /etc/systemd/system/*.wants/* \
          /lib/systemd/system/local-fs.target.wants/* \
          /lib/systemd/system/sockets.target.wants/*udev* \
          /lib/systemd/system/sockets.target.wants/*initctl* \
          /lib/systemd/system/basic.target.wants/* \
          /lib/systemd/system/anaconda.target.wants/*

RUN pip3 install $pip_packages \
 && sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/' /etc/sudoers \
 && mkdir -p /etc/ansible \
 && echo -e '[local]\nlocalhost ansible_connection=local' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup"]
CMD ["/usr/lib/systemd/systemd"]
