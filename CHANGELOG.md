# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project uses date-based versioning: `YYYYMMDD`, `YYYYMMDD.1`, `YYYYMMDD.2`, etc.

## [Unreleased]

### Added

- Initial release of multi-architecture Docker images for Ansible role testing
- Distros: `debian` (trixie-slim, bookworm-slim, bullseye-slim), `ubuntu` (noble, jammy, focal), `fedora` (41, 40, 39), `rockylinux` (9-minimal, 8-minimal), `amazonlinux` (2023, 2), `alpine` (3.21, 3.20, 3.19)
- Multi-arch support for `linux/amd64` and `linux/arm64` via Docker Buildx
- Ansible, Python 3, sudo, and `/etc/ansible/hosts` included in all images
- systemd included in Debian and Ubuntu images for service management testing
- CI workflow (lint → build → test) triggered on push and pull request
- Release workflow triggered on date-format tags, publishes to `ghcr.io/haydenk/docker-ansible-testing`
- Labeler workflow for automatic PR and issue labelling
- Dependabot configured for weekly GitHub Actions updates
- Issue templates for bug reports, feature requests, and questions
- Pull request template with gitflow checklist

[Unreleased]: https://github.com/haydenk/docker-ansible-testing/compare/20260309...HEAD
[20260309]: https://github.com/haydenk/docker-ansible-testing/releases/tag/20260309
