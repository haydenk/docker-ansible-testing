# docker-ansible-testing

Multi-architecture Docker images for testing [Ansible](https://www.ansible.com/) roles across a variety of Linux distributions. Each image includes Ansible, Python 3, and a minimal set of packages needed to run Ansible playbooks inside a container.

## Images

Images are published to the GitHub Container Registry at `ghcr.io/haydenk/docker-ansible-testing/<distro>:<version>`.

| Distro | Versions |
|--------|---------|
| `debian` | `trixie-slim`, `bookworm-slim`, `bullseye-slim` |
| `ubuntu` | `noble`, `jammy`, `focal` |
| `fedora` | `41`, `40`, `39` |
| `rockylinux` | `9-minimal`, `8-minimal` |
| `amazonlinux` | `2023`, `2` |
| `alpine` | `3.21`, `3.20`, `3.19` |

All images are built for `linux/amd64` and `linux/arm64`.

## Tags

Each image is published with two tags per release:

| Tag | Description |
|-----|-------------|
| `<version>` | Floating — always points to the latest release |
| `<version>-<release>` | Pinned — locked to a specific release date |

**Examples:**
```
ghcr.io/haydenk/docker-ansible-testing/debian:bookworm-slim
ghcr.io/haydenk/docker-ansible-testing/debian:bookworm-slim-20260309
```

Releases use date-based versioning: `YYYYMMDD`, `YYYYMMDD.1`, `YYYYMMDD.2`, etc.

## Usage

### Pull an image

```bash
docker pull ghcr.io/haydenk/docker-ansible-testing/debian:bookworm-slim
```

### Verify Ansible

```bash
docker run --rm ghcr.io/haydenk/docker-ansible-testing/debian:bookworm-slim ansible --version
```

### Run a playbook

```bash
docker run --rm \
  -v "$(pwd)":/ansible \
  ghcr.io/haydenk/docker-ansible-testing/debian:bookworm-slim \
  ansible-playbook /ansible/playbook.yml
```

### Use in a Molecule scenario

```yaml
# molecule/default/molecule.yml
driver:
  name: docker
platforms:
  - name: debian-bookworm
    image: ghcr.io/haydenk/docker-ansible-testing/debian:bookworm-slim
    pre_build_image: true
  - name: ubuntu-noble
    image: ghcr.io/haydenk/docker-ansible-testing/ubuntu:noble
    pre_build_image: true
  - name: fedora-41
    image: ghcr.io/haydenk/docker-ansible-testing/fedora:41
    pre_build_image: true
```

## What's included

Each image contains:

- `ansible` (latest via pip)
- `python3` and `pip`
- `sudo`
- `/etc/ansible/hosts` with a local inventory entry

Debian and Ubuntu images additionally include `systemd` for roles that require service management.

## Development

### Prerequisites

- Docker with Buildx
- QEMU (for multi-arch builds)

### Build an image locally

```bash
docker buildx build \
  --platform linux/amd64 \
  --build-arg VERSION=bookworm-slim \
  -t test/debian:bookworm-slim \
  ./debian
```

### Run the verification checks

```bash
docker run --rm test/debian:bookworm-slim ansible --version
docker run --rm test/debian:bookworm-slim python3 --version
docker run --rm test/debian:bookworm-slim test -f /etc/ansible/hosts
```

## Contributing

1. Fork the repository
2. Create a feature branch following git flow: `feature/<name>`
3. Make your changes — if adding or removing a distro or version, update `matrix.json`
4. Open a pull request against `develop`

See [CONTRIBUTING](CONTRIBUTING.md) for the full checklist.

## License

[GNU General Public License v3.0](LICENSE)
