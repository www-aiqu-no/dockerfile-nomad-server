# dockerfile-nomad-server
Dockerized image for running nomad server on e.g. K8S, docker-swarm or similar
Should only be used for running server-instances

The image is based on consul dockerfile, and uses glibc & pre-compiled nomad
binaries from official hashicorp releases

Agent is started as non-root user (nomad), and uses su-exec and dumb-init
