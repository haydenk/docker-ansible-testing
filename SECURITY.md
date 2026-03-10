# Security Policy

## Supported Versions

Only the latest release is actively supported with security fixes.

| Version | Supported |
|---------|-----------|
| Latest  | ✅ |
| Older   | ❌ |

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

Report vulnerabilities privately using [GitHub's private vulnerability reporting](https://github.com/haydenk/docker-ansible-testing/security/advisories/new).
This ensures the issue can be reviewed and a fix prepared before public disclosure.

Please include as much of the following as possible:

- A description of the vulnerability and its potential impact
- The affected image(s), distro, and version
- Steps to reproduce or a proof of concept
- Any suggested mitigations

You can expect an acknowledgement within **72 hours** and a status update within **7 days**.

## Disclosure Policy

Once a fix is available, the vulnerability will be disclosed via a GitHub Security Advisory
along with a patched release. Credit will be given to the reporter unless anonymity is requested.

## Scope

These are base images intended for use in CI/testing environments, not production deployments.
Please keep this context in mind when evaluating severity:

- Vulnerabilities in a specific distro's packages — report upstream to the distro's security team as well
- Vulnerabilities introduced by the Dockerfile itself (e.g. insecure configuration, overly broad permissions) — **in scope**
- Ansible or Python CVEs — report upstream; a new image release will be cut to pick up the fix
