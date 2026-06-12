# Chapter 4.1 – Official Images and Trust

## Overview
Docker images are the building blocks of containerized applications.  In this section we explore what makes an image "official", how Docker verifies that an image is trustworthy, and why relying on official images alone does not guarantee security.

Official images are pulled from the Docker Hub `library` repository.  They are curated by Docker maintainers and built from source code with a public review process.  Understanding the approval workflow, the trust chain, and how to verify image manifests are critical skills for any DevOps engineer.

---

## Learning Objectives
After completing this section you should be able to:

- Identify the difference between official, community, and custom images.
- Explain Docker’s official image approval process and how source tarballs are linked to Docker Hub layers.
- Verify an image’s integrity using digests and `docker image ls --digests`.
- Understand the `scratch` base image and the `ADD` instruction side‑effects.
- Point out common mistakes when using official images.

---

## Core Concepts
### Definition
**Official image** – an image in the Docker Hub `library` repository that has been built via a pull‑request process in the Docker Official Images GitHub organization and whose build scripts are publicly available.

### Explanation
Docker official images are like the “free‑for‑use” packages in a Linux distribution.  They come from a verified source, have well‑defined Dockerfiles, and are signed so you can be confident the image content matches the build recipe.

### Examples
- `ubuntu:24.04` – built from Ubuntu base tarballs pulled from Launchpad.
- `python:3.12-slim` – built from the CPython source tree.
- `alpine:3.20` – minimal Alpine Linux image.

### Diagrams
```mermaid
flowchart TD
    GitHub[Docker Official Images Repo] -->|PR approved| Build[Github Actions]
    Build -->|Build image| DockerHub[Docker Hub
        library<sub>official images</sub>]
    DockerHub -->|Push layer| Registry[Registry (signed)]
```

---

## Architecture / Workflow
Docker official image builds start from a base image such as `scratch`.  The Dockerfile fetches source tarballs (often `*.tar.gz`) via `ADD`.  Because the tarball is in the same archive format, Docker automatically extracts it onto the container root.

### Workflow Steps
1. PR is opened in `docker-library/official-images`.
2. CI checks fetch source, build Dockerfile, run tests.
3. On success a new image layer is pushed to Docker Hub.
4. Docker Hub tags the image with SHA‑256 digest and signs the manifest.

---

## Commands Learned
```bash
# Pull an official image and show its digest
docker pull ubuntu:24.04

# List digests for all tags of a repository
docker image ls --digests ubuntu

# Inspect image layers and compare to Dockerfile
docker image history --no-trunc ubuntu:24.04
```

### Command Reference
| Command | Purpose |
|---------|---------|
| `docker pull <image>` | Fetches image from registry |
| `docker image ls --digests` | Shows image digests for verification |
| `docker image history` | Displays build layers and timestamps |

---

## Practical Examples
### Example 1 – Verify an image’s authenticity
```bash
# Pull image
sudo docker pull ubuntu:24.04
# List digests
sudo docker image ls --digests ubuntu | grep 24.04
```
**Expected output** (sample):
```text
ubuntu   24.04   8a1e5f2b...    6 hours ago   123MB
```
The SHA‑256 shown must match the digest reported on Docker Hub.

### Example 2 – Build the official Ubuntu image from source
```bash
git clone https://github.com/docker-library/ubuntu.git
cd ubuntu
docker build -t my-ubuntu:24.04 .
```
You should see the same layers and history as the official `ubuntu:24.04` image.

---

## Quick Revision
- Official images come from Docker Hub `library` and are built via a public PR workflow.
- They use `scratch` as the base; `ADD .tar.gz` will automatically unpack.
- Always verify the image digest against Docker Hub to ensure the image matches the build.
- Running containers as root is discouraged; use `USER` in the Dockerfile.
- Mixing community images with official ones can blind you to hidden vulnerabilities.

---

## Interview Questions
### Q1. What makes an image “official” in Docker terms?
**Answer:** It is part of the `library` repository on Docker Hub, built through the Docker Official Images GitHub PR process, and its Dockerfile and source code are publicly available.

### Q2. How can you verify the integrity of a pulled image?
**Answer:** Pull the image, then run `docker image ls --digests` and compare the SHA‑256 digest to the one shown on Docker Hub or in the image’s metadata. You can also use `docker image inspect` to check the `RepoDigests` field.

### Q3. Why is running a container as root considered risky?
**Answer:** A root container can read/write any host file that is bind‑mounted, can bind‑listen on privileged ports, and can escape the container more easily if a vulnerability is exploited.

---

## Common Mistakes
- Assuming an official image is inherently safe without checking the digest.
- Not validating the source tarball checksums or trusting the default `docker` tags.
- Forgetting to set `USER` in Dockerfile, leaving containers running as root.
- Using `FROM scratch` followed by `ADD` without understanding the unpacking behavior.
- Reusing an older image tag without pulling the latest digest.

---

## References
- [MOOC.fi Course Material: Official Images and trust](https://courses.mooc.fi/org/uh-cs/courses/devops-with-docker-spring-2026/chapter-4/official-images-and-trust)
- [Docker Official Images Documentation](https://docs.docker.com/reference/dockerfile/official-images/)
- [Docker Hub Library](https://hub.docker.com/u/library)
- [Docker Hub Image Layer Security](https://docs.docker.com/registry/insecure/)

---

## Key Takeaways
- Official images are curated, documented, and signed—yet vulnerability still exists.
- Always verify digests; never trust `latest` or unverified tags.
- Non‑root is a best practice; add `USER` to Dockerfile.
- The build pipeline is public; you can build the image locally to compare.
