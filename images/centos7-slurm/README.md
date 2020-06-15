# Centos7 SLURM image

## Build the image

```
docker build --build-arg SLURM_VERSION=20.02.2 -t miguelgila/centos7-slurm:20.02.2 .
docker push miguelgila/centos7-slurm:20.02.2
```
