ARG VERSION=3.21

FROM --platform=$TARGETPLATFORM alpine:${VERSION}

ARG VERSION
LABEL maintainer="Hayden King" distro=alpine version=${VERSION}

ENV pip_packages="ansible"

# hadolint ignore=DL3018,DL3013
RUN apk --no-cache add \
      python3 \
      python3-dev \
      py3-pip \
      openrc \
      sudo \
      bash \
      curl \
      openssh-client \
      rsync \
      iproute2 \
 && pip3 install --no-cache-dir --break-system-packages $pip_packages \
 && mkdir -p /etc/ansible \
 && printf '[local]\nlocalhost ansible_connection=local\n' > /etc/ansible/hosts

CMD ["/bin/sh"]
