# Contributing

Thanks for taking the time to contribute! Below are the guidelines for working on this project.

## Table of Contents

- [Getting Started](#getting-started)
- [Branch Structure](#branch-structure)
- [Types of Contributions](#types-of-contributions)
  - [Adding a Distro Version](#adding-a-distro-version)
  - [Adding a New Distro](#adding-a-new-distro)
  - [Bug Fixes](#bug-fixes)
- [Local Development](#local-development)
- [Commit Style](#commit-style)
- [Pull Request Process](#pull-request-process)
- [Code of Conduct](#code-of-conduct)

---

## Getting Started

1. Fork the repository and clone your fork
2. Ensure you have Docker with Buildx and QEMU installed for multi-arch builds

```bash
docker buildx create --use
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

---

## Branch Structure

This project follows [git flow](https://nvie.com/posts/a-successful-git-branching-model/):

| Branch | Purpose |
|--------|---------|
| `master` | Latest stable release |
| `develop` | Integration branch — all features merge here first |
| `feature/<name>` | New features or distro additions |
| `bugfix/<name>` | Bug fixes targeting `develop` |
| `hotfix/<name>` | Critical fixes targeting `master` directly |
| `release/<version>` | Release preparation |

**All pull requests should target `develop`**, except hotfixes which target `master`.

---

## Types of Contributions

### Adding a Distro Version

1. Create a `feature/<distro>-<version>` branch from `develop`
2. Add the new entry to `matrix.json`:
   ```json
   { "distro": "debian", "version": "forky-slim", "platforms": "linux/amd64,linux/arm64" }
   ```
3. Test the build locally (see [Local Development](#local-development))
4. Open a pull request against `develop`

### Adding a New Distro

1. Create a `feature/<distro>` branch from `develop`
2. Create a `<distro>/Dockerfile` following the conventions of existing Dockerfiles:
   - Use `ARG VERSION=<default>` before `FROM`
   - Use `FROM --platform=$TARGETPLATFORM <distro>:${VERSION}`
   - Re-declare `ARG VERSION` after `FROM`
   - Include a single `LABEL` with `maintainer`, `distro`, and `version`
   - Install Ansible via pip
   - Create `/etc/ansible/hosts` with a local inventory entry
3. Add all desired versions to `matrix.json`
4. Add the distro name to the `hadolint` matrix in `.github/workflows/ci.yml`
5. Add label rules to `.github/labeler.yml`
6. Open a pull request against `develop`

### Bug Fixes

1. Create a `bugfix/<name>` branch from `develop` (or `hotfix/<name>` from `master` for critical issues)
2. Make your fix
3. Test locally
4. Open a pull request

---

## Local Development

### Build a single image

```bash
docker buildx build \
  --platform linux/amd64 \
  --build-arg VERSION=bookworm-slim \
  --load \
  -t test/debian:bookworm-slim \
  ./debian
```

### Verify the image

```bash
docker run --rm test/debian:bookworm-slim ansible --version
docker run --rm test/debian:bookworm-slim python3 --version
docker run --rm test/debian:bookworm-slim test -f /etc/ansible/hosts && echo "inventory ok"
```

### Build multi-arch (no load, push to registry or use --output)

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  --build-arg VERSION=bookworm-slim \
  ./debian
```

### Lint Dockerfiles

```bash
docker run --rm -i hadolint/hadolint < debian/Dockerfile
```

---

## Commit Style

Use short, descriptive commit messages in the imperative mood:

```
add fedora 42 to matrix
fix rockylinux 8 python pip install
update debian bullseye to use --break-system-packages
```

---

## Pull Request Process

1. Ensure your branch is up to date with `develop` before opening a PR
2. Fill out the pull request template completely
3. If you added or removed a distro or version, confirm `matrix.json` is updated
4. The CI pipeline (lint → build → test) must pass before merging
5. A maintainer will review and merge your PR

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).
By participating, you agree to uphold these standards.
