# Slurm development environment in containers

This is my personal Slurm development environment with containers. At any given time things might not work, so use at your own risk!

* [`docker-compose/`](docker-compose/README.md) contains my development environment for Docker Compose as I run it on my laptop.
* [`k8s/`](k8s/README.md) is another instance of my development environment for Kubernetes, that I also run on my laptop using [kind](https://kind.sigs.k8s.io), but that I ocasionally utilize on other, remote, kubernetes clusters.
* [`images/centos7-slurm/`](images/centos7-slurm/README.md) is my Centos7-based Slurm docker repository. The idea is to have more images as things change (for example, I want to do the same with a SLES12-based image).