# AICloud-GM
Test AI cloud

### Running docker image on the host:
```
nvidia-docker run -d -p 37001:22 --name tflow_build -v /media:/media gm-tf-2.7:0.0
nvidia-docker run -d -p 37001:22 --name cloud-hub -v /media:/media cloud_hub:0.0
```
Where: -d - >> run docker image detached, othervise use -it (will not work with gm-tf-2.7:0.0 image)

--name= >> assign name to running container

-p 37001:22 >> redirect ssh 22 host port to 37000 ssh container port

-v /media:/media >> make media folder inside host available in docker container

### Enter to the docker image:
`docker-enter tflow_build`

`docker exec -it tflow_build /bin/bash`
### In order to run shell commands after using docker-enter method, please run following command:
`export TERM=xterm`
### Running application inside docker container:
`docker exec -it tflow_build python /gpu_tf_check.py`
`docker exec -it tflow_build python -c "import tensorflow; print(tensorflow.__version__)" > tflow.txt`
### Enter to the running container (for administrative purposes, cannot run tensorflow in this mode)
```
docker ps
PID=$(docker inspect --format '{{.State.Pid}}' tflow_build)
sudo nsenter --target $PID --mount --uts --ipc --net --pid
```

