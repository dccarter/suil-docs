---
title: Developing With Docker
weight: -10
---

{{< toc >}}

# Development With Docker
The good news is that this project includes a prebuilt container with all dependencies pre-installed that can be used for development or continuous integration.
## Useful Docker Images
The following docker images will help when developing `SCF` or with developing with `SCF`. Their Dockerfiles are currently hosted in a [GitLab Group](https://gitlab.com/sw-devel/images) and have minimal CI. I plan to move them to GitHub at some point

- **[`suilteam/base`](https://hub.docker.com/r/suilteam/base)**
  This image can be used in CI environments to build `SCF` project. The image includes all the libraries and tools needed to build the project. Projects depending on `SCF` can have their docker images depend on this image. The image is hosted at [Dockerfile Repo](https://gitlab.com/sw-devel/images/base). The image has the following tags;
  - `suilteam/base:ubuntu` for developing on Ubuntu
  - `suilteam/base:alpine` for developing on Alpine

- **[`suilteam/remote:ubuntu`](https://hub.docker.com/r/suilteam/remote)**
  This image extends the `suilteam/base:ubuntu` image by adding openssh server. The idea here is that this can actually be used for remote development and we can install anything we might need to make development easier. This is the image I currently use when developing on Mac OS with CLion IDE or VS Code Remote-Containers environment

## Visual Studio Code Remote Development
This project is setup up with Visual Studio Code Remote Development. If one has visual studio code and docker installed, they can launch the remote development environment in seconds and start developing and building.
The environment will launch 3 containers
- Redis and PostgreSQL container
- A `devel` container which is the container we are actually developing on. The container features the following defaults;
  - built on top of `suilteam/remote:ubuntu` 
  - `zsh` and `oh-my-zsh` installed and set as default shell
  - Memory limit of `16g`
  - Exposes ports `8000`-`8099`

See [Visual Studio Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)