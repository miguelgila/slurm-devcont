# docker-compose

This is my personal development Slurm container setup based on CentOS 7 and docker-compose. Forked off the great [bikerdanny/docker-centos-slurm](https://github.com/bikerdanny/docker-centos-slurm) repo.

TODO: 
- [ ] Fix Slurm Topology
- [ ] Add more compute nodes

## Pre-requisites

* j2cli available on the host system
```
pip3 install j2cli
```

* Make sure you have the correct image locally as defined in [`config.ini`](config.ini)
```
$ grep ^image config.ini
image=miguelgila/centos7-slurm:20.02.2
```
Refer to [the images section](../images/centos7-slurm/README.md) to get information about this particular image named `miguelgila/centos7-slurm:20.02.2`

## Environment creation

* Generate the Docker Compose manifest
```
j2 docker-compose.yml.j2 config.ini > docker-compose.yml
```

* Create needed dependencies
```
mkdir var_lib_mysql
mkdir etc_munge
mkdir etc_slurm
mkdir shared
```

## Start up

* Launch the docker containers with `docker-compose`
```
docker-compose up -d
docker-compose exec c10 bash
sinfo
```

* Optionally, get the topology right:
```
supervisorctl stop slurmctld
echo "SwitchName=s2 Nodes=c10" > /etc/slurm/topology.conf #FIXME this is ugly, needs to be improved
echo "#
# TOPOLOGY
TopologyPlugin=topology/tree" >> /etc/slurm/slurm.conf #FIXME this is ugly
sacctmgr show cluster # if the command returns successfully, you can start slurmctld again
supervisorctl start slurmctld
sinfo
```

## Teardown
```
docker-compose down
docker volume rm docker-compose_shared  docker-compose_var_lib_mysql docker-compose_etc_slurm docker-compose_etc_munge
```

## Using it

I mainly use this with Visual Studio Code, putting in `shared/` all the plugins and git repositories I need to test. The biggest advantage to the k8s version of this is the persistance, which allows you to configure VSC to automatically fire up these containers if you want to have an integrated environment to code and run/debug within it.

EOF
