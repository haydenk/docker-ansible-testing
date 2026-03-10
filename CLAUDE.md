# docker-ansible-testing

Docker images for testing Ansible roles across multiple Linux distributions.
Multi-architecture (linux/amd64, linux/arm64) using Docker BuildKit.

## Project Structure

```
docker-ansible-testing/
├── .github/
│   └── workflows/
│       └── build.yml        # GitHub Actions build matrix
├── alpine/Dockerfile
├── amazonlinux/Dockerfile
├── debian/Dockerfile
├── fedora/Dockerfile
├── rockylinux/Dockerfile
└── ubuntu/Dockerfile
```

## Dockerfiles

### Conventions
- Single `ARG VERSION` is the only build argument — it is the full upstream image tag suffix
  (e.g. `bookworm-slim`, `9-minimal`, `noble`). Slim/minimal is encoded in VERSION, not a separate arg.
- `FROM --platform=$TARGETPLATFORM` on all images for multi-arch support.
- `LABEL maintainer="Hayden King" distro=<name> version=${VERSION}` on every image.
- `&&` chaining throughout all `RUN` commands — never `;`.
- `ENV pip_packages="ansible ..."` used for pip installs.
- Ansible inventory written to `/etc/ansible/hosts` with `localhost ansible_connection=local`.

### Sources
Based on [geerlingguy's docker-*-ansible repos](https://github.com/geerlingguy).
Each Dockerfile documents its source repo and differences in the header comments.
Lines adopted from newer geerlingguy versions are noted with `# From: <repo-url>`.

### Default Versions (latest stable)

| Distro      | Default VERSION  | Slim/minimal variant |
|-------------|-----------------|----------------------|
| debian      | `bookworm-slim` | `-slim` suffix; use `bookworm`, `trixie-slim`, etc. |
| ubuntu      | `noble`         | No slim variant available |
| fedora      | `41`            | No slim variant available |
| rockylinux  | `9-minimal`     | `-minimal` suffix; use `9` for full image |
| amazonlinux | `2023`          | No slim/minimal variant available |
| alpine      | `3.21`          | Already minimal |

### Distro-Specific Notes

**debian**
- Slim variant available (`-slim` suffix).
- `--break-system-packages` on both pip upgrade and install (adopted from debian13/trixie).
- `EXTERNALLY-MANAGED` removed via wildcard in the apt install layer.

**ubuntu**
- No slim variant available.
- Uses named releases: `noble` (24.04), `jammy` (22.04), `focal` (20.04).
- `EXTERNALLY-MANAGED` removed via wildcard in the apt install layer.

**fedora**
- No slim variant available.
- Uses `dnf` (not `dnf5`) for compatibility with fedora 39/40; `dnf` is an alias for `dnf5` on 41+.
- Python bindings fallback: `dnf -y install python3-libdnf5 || dnf -y install python3-dnf`
  (`python3-libdnf5` on 41+, `python3-dnf` on 39/40).
- `max_parallel_downloads=20` in `/etc/dnf/dnf.conf` (valid on all fedora versions; sourced from fedora43 repo).

**rockylinux**
- Minimal variant available (`-minimal` suffix).
- Uses `microdnf` to bootstrap `dnf` on minimal images.
- Rocky Linux 10 uses a namespaced image (`rockylinux/rockylinux:10`); the FROM line will need
  updating if version 10 support is added to the matrix.

**amazonlinux**
- No slim/minimal variant available.
- pip upgrade omitted — breaks on AL2023.

**alpine**
- Uses OpenRC, not systemd. `CMD ["/bin/sh"]`.
- `--break-system-packages` required for pip on 3.21+.
- No geerlingguy source repo exists; Dockerfile is custom.

### Removed from source (intentional)
- `initctl_faker` — arch-specific compiled binary; breaks multi-arch builds. Not needed on
  modern Ubuntu/Debian (20.04+/11+) where systemd has replaced SysV init hooks.
- `ENV container=docker` — legacy hint for systemd; modern systemd (Fedora 39+, Rocky 9+)
  detects containers automatically via cgroup namespaces.

## GitHub Actions

**Workflow:** `.github/workflows/build.yml`
**Registry:** `ghcr.io/${{ github.repository_owner }}/<distro>:<version>`
**Platforms:** `linux/amd64,linux/arm64` (defined in `env.PLATFORMS`)

Images are pushed on `push` to `main` and `workflow_dispatch`. Pull requests build only (no push).
BuildKit layer cache is scoped per `distro-version`.

### Current Matrix

```yaml
- { distro: "debian",      version: "trixie-slim",   platforms: "linux/amd64,linux/arm64" }
- { distro: "debian",      version: "bookworm-slim",  platforms: "linux/amd64,linux/arm64" }
- { distro: "debian",      version: "bullseye-slim",  platforms: "linux/amd64,linux/arm64" }
- { distro: "ubuntu",      version: "noble",          platforms: "linux/amd64,linux/arm64" }
- { distro: "ubuntu",      version: "jammy",          platforms: "linux/amd64,linux/arm64" }
- { distro: "ubuntu",      version: "focal",          platforms: "linux/amd64,linux/arm64" }
- { distro: "fedora",      version: "41",             platforms: "linux/amd64,linux/arm64" }
- { distro: "fedora",      version: "40",             platforms: "linux/amd64,linux/arm64" }
- { distro: "fedora",      version: "39",             platforms: "linux/amd64,linux/arm64" }
- { distro: "rockylinux",  version: "9-minimal",      platforms: "linux/amd64,linux/arm64" }
- { distro: "rockylinux",  version: "8-minimal",      platforms: "linux/amd64,linux/arm64" }
- { distro: "amazonlinux", version: "2023",           platforms: "linux/amd64,linux/arm64" }
- { distro: "amazonlinux", version: "2",              platforms: "linux/amd64,linux/arm64" }
- { distro: "alpine",      version: "3.21",           platforms: "linux/amd64,linux/arm64" }
- { distro: "alpine",      version: "3.20",           platforms: "linux/amd64,linux/arm64" }
- { distro: "alpine",      version: "3.19",           platforms: "linux/amd64,linux/arm64" }
```

## Building Locally

```bash
# Build with default version
docker buildx build --platform linux/amd64,linux/arm64 -t ansible-test:debian debian/

# Build a specific version
docker buildx build --platform linux/amd64,linux/arm64 \
  --build-arg VERSION=trixie-slim \
  -t ansible-test:trixie-slim debian/

# Build for local platform only (faster for testing)
docker build --build-arg VERSION=noble -t ansible-test:noble ubuntu/
```
