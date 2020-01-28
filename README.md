# dockerfile-nomad-server
![Docker Cloud Automated build](https://img.shields.io/docker/cloud/automated/aiqu/nomad-server)
![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/aiqu/nomad-server)
![GitHub last commit](https://img.shields.io/github/last-commit/www-aiqu-no/dockerfile-nomad-server)

### Docker Hub
[https://hub.docker.com/r/aiqu/nomad-server](https://hub.docker.com/r/aiqu/nomad-server)
```bash
# ubuntu version
docker pull aiqu/nomad-server
```
```bash
# alpine version
docker pull aiqu/nomad-server:alpine
```
### Description (Hashinetes revisited)
Dockerized image for running nomad server on e.g. on K8S, docker-swarm, docker-compose or similar. This image is for running server instances (control-pane) of Nomad. Run your clients natively.

If you want to use Nomad & Kubernetes, it is possible to run the control-pane of nomad as kubernetes deployments.

The image is based on the official consul dockerfile, and is built on two distibutions. Both use the official pre-compiled nomad hashicorp releases:

1. Alpine Linux: adds glibc, su-exec & dumb-init
2. Ubuntu: Multi-stage build, and adds gosu, su-exec

The agent runs as a non-root user (nomad)

### Example use-cases
- You are running Nomad deployment locally, but want to try out/migrate some services to kubernetes
- You are using kubernetes, but have some tasks that can't run on your cluster. Nomad runs anything you throw at it
- You don't want to manage applications natively (e.g. bash, ansible, salt, puppet, etc), but still want to run a Nomad cluster
- You want to have som fun!

### Videos
- Hashinetes! (by Kelsey Hightower) [ [Video](https://www.hashicorp.com/resources/hashinetes-combining-kubernetes-hashicorp-kelsey-hightower) | [Git](https://github.com/kelseyhightower/nomad-on-kubernetes) ]

### Links
- https://www.nomadproject.io/
- https://hub.docker.com/
- https://shields.io

### Todo
- Create helm-chart, based on official Consul chart (or hope Hashicorp will make one..)
- Automated testing (docker builds)
- Documentation
