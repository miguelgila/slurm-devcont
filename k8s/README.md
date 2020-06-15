# k8s

This is my personal Slurm development container setup based on CentOS 7 and Kubernetes. Forked off the great [bikerdanny/docker-centos-slurm](https://github.com/bikerdanny/docker-centos-slurm) and my previous [miguelgila/docker-centos-slurm](https://github.com/miguelgila/docker-centos-slurm) repo. I use it to test CI/CD workflows when developing SPANK plugins (or other cool mods to Slurm), but feel free to do anything that you like with it.

NOTE: For now all there is only one service and compute node named `c10`, where `slurmctld` and `slurmd` run.

TODO: 
- [ ] Fix Slurm Topology
- [ ] Add more compute nodes
- [ ] Add CI/CD information

## Pre-requisites

I am assuming that this is running locally or on a GitHub Action environment using [kind](https://kind.sigs.k8s.io), which is a pretty neat tool that can be used to create k8s clusters on-demand using Docker containers instead of VMs. However, most of what is written here can be used on a regular Kubernetes cluster, minus the `kind` commands, of course :nerd_face:

* Install Kind
```
brew install kind
```

* j2cli available on the host system
```
pip3 install j2cli
```

* Make sure you have the correct image locally as defined in [`config.ini`](config.ini)
```
$ grep ^image config.ini
image=miguelgila/centos7-slurm:20.02.2
```
Refer to [the images section](../images/centos7-slurm/README.md) to get information about  this particular image named `miguelgila/centos7-slurm:20.02.2`


## Environment creation

* Generate the Kubernetes manifests for mariadb and c10 node:
```
j2 mariadb-deployment.yaml.j2 config.ini > mariadb-deployment.yaml
j2 c10-deployment.yaml.j2 config.ini > c10-deployment.yaml
```

## Start up


* Create a new Kind Kubernetes cluster:
```
kind create cluster
```

* Push the image to the Kind cluster (or to docker hub if you wish):
```
kind load docker-image miguelgila/centos7-slurm:20.02.2 
```

* Launch the deployments and services with `kubectl`
```
kubectl apply -f ./mariadb-deployment.yaml
kubectl apply -f ./mariadb-service.yaml
kubectl apply -f ./c10-deployment.yaml
k exec -ti c10-d55cd6cc-fzl2s bash
sinfo
```

## Teardown 
```
kubectl delete deployment mariadb
kubectl delete service mariadb
kubectl delete deployment c10
kind delete cluster
```

## Using it

I mainly use this with `kind` and because there is no persistance, I just pull all my git repos manually and run testing with the pods running. Once I'm done testing what I need to do, I run the teardown explained before.

```
k exec -ti c10-d55cd6cc-fzl2s bash
git clone ....
```

EOF