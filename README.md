# dockerfile-nomad-server
![Docker Cloud Automated build](https://img.shields.io/docker/cloud/automated/aiqu/nomad-server)
![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/aiqu/nomad-server)
![GitHub last commit](https://img.shields.io/github/last-commit/www-aiqu-no/dockerfile-nomad-server)

Download from [Docker Hub](https://hub.docker.com/r/aiqu/nomad-server):
```bash
docker pull aiqu/nomad-server:<version>
```

## Description
Dockerized image for running nomad server on e.g. K8S, docker-swarm or similar
Should only be used for running server-instances

The image is based on consul dockerfile, and uses glibc & pre-compiled nomad
binaries from official hashicorp releases

Agent is started as non-root user (nomad), and uses su-exec and dumb-init

## References
- https://www.nomadproject.io/
- https://hub.docker.com/
- https://shields.io
