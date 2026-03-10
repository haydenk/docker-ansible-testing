ARG VERSION=41

FROM --platform=$TARGETPLATFORM fedora:${VERSION}

ARG VERSION
LABEL maintainer="Hayden King" distro=fedora version=${VERSION}

ENV pip_packages="ansible"

# hadolint ignore=DL3041,DL3003
RUN echo "max_parallel_downloads=20" >> /etc/dnf/dnf.conf \
 && dnf -y update \
 && dnf -y install systemd \
 && dnf clean all && \
  (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ "$i" = systemd-tmpfiles-setup.service ] || rm -f "$i"; done); \
  rm -f /lib/systemd/system/multi-user.target.wants/*;\
  rm -f /etc/systemd/system/*.wants/*;\
  rm -f /lib/systemd/system/local-fs.target.wants/*; \
  rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
  rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
  rm -f /lib/systemd/system/basic.target.wants/*;\
  rm -f /lib/systemd/system/anaconda.target.wants/*;

# hadolint ignore=DL3041
RUN dnf -y install python3-pip sudo which python3-rpm \
  && (dnf -y install python3-libdnf5 || dnf -y install python3-dnf) \
  && dnf clean all

# hadolint ignore=DL3013
RUN pip3 install --no-cache-dir $pip_packages \
 && sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/' /etc/sudoers \
 && mkdir -p /etc/ansible \
 && printf '[local]\nlocalhost ansible_connection=local\n' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/usr/sbin/init"]
