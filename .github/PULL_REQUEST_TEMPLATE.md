## Description

<!-- What does this PR do? Summarise the change and link the related issue. -->

Closes #

---

## Type of change

- [ ] Bug fix &nbsp;&nbsp;(`bugfix/<name>`)
- [ ] New feature / distro &nbsp;&nbsp;(`feature/<name>`)
- [ ] Hotfix &nbsp;&nbsp;(`hotfix/<name>`)
- [ ] Release prep &nbsp;&nbsp;(`release/<version>`)
- [ ] Documentation / chore

## Changes

<!-- Bullet-point summary of what changed. -->

-

## Testing

<!-- How was this verified? What should reviewers specifically check? -->

- [ ] Built locally with `docker buildx build`
- [ ] Verified `ansible --version` runs in the image
- [ ] Verified `python3 --version` runs in the image
- [ ] Verified `/etc/ansible/hosts` exists in the image

## Checklist

- [ ] Branch follows gitflow naming (`feature/`, `bugfix/`, `hotfix/`, `release/`)
- [ ] `matrix.json` updated if a distro or version was added or removed
- [ ] All affected Dockerfiles updated consistently
