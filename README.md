[![](https://img.shields.io/badge/dwclass-dev--004--git-brightgreen?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/README.md)
[![](https://img.shields.io/badge/dwclass-dev--005--git-brightgreen?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/README.md)
[![](https://img.shields.io/badge/dwclass-dev--006--git-brightgreen?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/README.md)
[![GitHub license](https://img.shields.io/github/license/mi-alkhamis/docker-cheatsheet?color=ff1020&style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/LICENSE)
[![GitHub forks](https://img.shields.io/github/forks/mi-alkhamis/docker-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/network)
[![GitHub issues](https://img.shields.io/github/issues/mi-alkhamis/docker-cheatsheet?color=blue&style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/issues)
[![GitHub stars](https://img.shields.io/github/stars/mi-alkhamis/docker-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/stargazers)


# Docker Cheatsheet

## Intro

in this cheat sheet, we review the most used docker commands with their examples.



## Requirements

  You should have at least docker 20 :smile: !!!



## Commands

- Run Nginx container and show Docker version and exposed ports

  ``` console
  milad@docker:~$ docker pull nginx
  Using default tag: latest
  latest: Pulling from library/nginx
  Digest: sha256:4d4d96ac750af48c6a551d757c1cbfc071692309b491b70b2b8976e102dd3fef
  Status: Image is up to date for nginx:latest
  docker.io/library/nginx:latest
  milad@docker:~$ docker inspect nginx:latest |jq .[0].DockerVersion
  "20.10.7"
  milad@docker:~$ docker inspect nginx:latest |jq .[0].Config.ExposedPorts
  {
    "80/tcp": {}
  }
  ```

  

- Make an new image from Nginx

  ```console
  milad@docker:~$ docker save -o nginx.tar.gz nginx
  milad@docker:~$ docker rm -f nginx 
  nginx
  milad@docker:~$ docker import nginx.tar.gz  nginx:v1.20.0-test
  sha256:87391bef9551b8bc6d4995399f4e5fa91ccffcef76abe53c402f54682fd3fb75
  milad@docker:~$ docker images
  REPOSITORY         TAG            IMAGE ID       CREATED          SIZE
  nginx              v1.20.0-test   87391bef9551   5 seconds ago    137MB
  nginx              latest         dd34e67e3371   8 days ago       133MB
  ```

  

- Publish 8081 port to Nginx container

  ```console
  milad@docker:~$ docker run --rm -dit -p 8081:80 --name nginx nginx
  c836cd3ae2f72d68ea66192f01073608154bd065b2b5a16be93cb3861c0bd61c
  ```

  

- Show processes of conatainer

  ```console
  milad@docker:~$ docker stats
  CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O         PIDS
  c836cd3ae2f7   nginx     0.00%     6.836MiB / 980.6MiB   0.70%     2.35kB / 1.84kB   13.9MB / 16.4kB   5
  ```

  Or

  ```console
  milad@docker:~$ docker top nginx
  UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
  root                8307                8285                0                   09:32               pts/0               00:00:00            nginx: master process nginx -g daemon off;
  systemd+            8377                8307                0                   09:32               pts/0               00:00:00            nginx: worker process
  systemd+            8378                8307                0                   09:32               pts/0               00:00:00            nginx: worker process
  systemd+            8379                8307                0                   09:32               pts/0               00:00:00            nginx: worker process
  systemd+            8380                8307                0                   09:32               pts/0               00:00:00            nginx: worker process
  ```

  

- remove dangling images

  `docker image prune`

  `docker image rm image-name`

 - first, create a container 

  ```console
  milad@docker:~$ docker run  -dit --name alpine alpine
  16afe7be3cb98004a4deb6411d470e04e1c0dea2a04b3047c3e239b7ef87accf
  ```
  - check the running container
  
  ```console
  milad@docker:~$ docker ps
  CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
  16afe7be3cb9   alpine    "/bin/sh"   5 seconds ago   Up 3 seconds             alpine
  ```
  
   - Send KILL signal to the container
   
   
  ``` console
  milad@docker:~$ docker kill -s SIGKILL alpine 
  ```
   
    
- Check container
  
    ```console
    milad@docker:~$ docker ps -a
    CONTAINER ID   IMAGE      COMMAND     CREATED          STATUS                       PORTS     NAMES
    16afe7be3cb9   alpine     "/bin/sh"   20 seconds ago   Exited (137) 6 seconds ago             alpine 
      ```
  
    

- Create an image from the running container

  - Run a container of alpine

    ```console
    
    milad@docker:~$ docker run --rm -dit --name alpine alpine 
    c39b54f5050b09dc5f56a7a3ef4141bf764fdd4c3cea3f911483fbd6d97ee4c6
    
    ```

  - Install NGINX on it (or anything you want)

    ```console
    milad@docker:~$ docker exec -it alpine sh
    / # apk add nginx
    fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz
    fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz
    (1/2) Installing pcre (8.44-r0)
    (2/2) Installing nginx (1.20.1-r3)
    Executing nginx-1.20.1-r3.pre-install
    Executing nginx-1.20.1-r3.post-install
    Executing busybox-1.33.1-r3.trigger
    OK: 7 MiB in 16 packages
    
    ```

  - Create a new image  by commit command from the container

    ```console
    milad@docker:~$ docker commit alpine  my-nginx
    sha256:c044d636a3b1005a79ba56e20b4f92e4f93fd6f254aa34820351dbe10ffc4eb4
    ```

  - List Images

  ```console
  milad@docker:~$ docker images
  REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
  my-nginx           latest    c044d636a3b1   5 seconds ago   9.18MB
  alpine-nginx       0.1       c623dc02116a   27 hours ago    9.18MB
  ```

- Pause a container

  ```console
  milad@docker:~$ docker container pause alpine 
  alpine
  milad@docker:~$ docker ps
  CONTAINER ID   IMAGE     COMMAND     CREATED          STATUS                   PORTS     NAMES
  d915171187f6   alpine    "/bin/sh"   23 seconds ago   Up 20 seconds (Paused)             alpine
  ```
  
- Get a Container ID with `jq` command

  ```console
  milad@docker:~$ docker container inspect  redis |jq .[0].Id
  "fa528402d91bb18b2b7fe2e3ac4180fb2383469d840b30f87b57b976747fedca"
  ```

- Limit container resources

  ```console
  milad@docker:~$ docker run -d --rm --cpus 0.5 --cpuset-cpus 0,2 --memory 512mb --memory-swap 768mb --name nginx-cpu nginx
  
  ```

- Use environments variables

  ```console
  milad@docker:~$ docker run --rm -dit -e CLASS=dws -e NAME=milad --name alpine alpine
  d3b7bd5982c491c01f38cd0883708839b2d39360350f8539d52219be9ebdc700
  milad@docker:~$ docker exec -it alpine sh
  / # env
  CLASS=dws
  HOSTNAME=d3b7bd5982c4
  SHLVL=1
  HOME=/root
  NAME=milad
  TERM=xterm
  PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
  PWD=/
  / # 
  ```
  
- Mount `/tmp` on `/data/` in `mount-volume` container 

  ```console
  milad@docker:~$ docker run --rm -dit -v /tmp:/data -w /data  --name mount-volume alpine
  ee16d9b91f641841da3bd8f88dec3180d7dde8a424a8ed2a2d538735ada54a7f
  milad@docker:~$ docker exec -it mount-volume sh
  /data # ls
  snap.lxd                 
  systemd-private-8uEIhg   
  systemd-private-8
  /data # 
  
  ```

## Some Dcoker file  topics 

**What's the Dockerfile**

  A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create
  an automated build that executes several command-line instructions in succession.

**Create a image from Commit 🆚 From Dockerfile**

we can create a new image from both of them, but Dockerfile is the best way to create a new image

**ENV 🆚 ARG**

ENV, Set environment variables in the container created from that image
ARG, like ENV is environment variables, but use when docker build a new image from the docker file

**ENTRYPOINT 🆚 CMD**

ENTRPOINT use to run the main app when container run, CMD takes an argument of it, but if ENTRYPOINT doesn't exist in the docker file CMD takes the main command and its argument

**Expose 🆚 Publish**

EXPOSE: to declare which ports use in the app
PUBLISH: to bind a port to the exposed one, to have access to it from the outside of a container

**RUN 🆚 ENTRYPOINT**

Both of them takes commands to run them, 
RUN, takes care of it when docker is building  an image, but ENTRYPOINT run a command when the container is starting up

**ADD 🆚 COPY**

both of them. use to add files to images, but add can able to handle URLs and also extract compressed files before adding them to the image 

**FROM alpine:latest**

Use alpine, latest version rootfs in an image, apps fell they run in alpine Linux 

**VOLUME /data**

tells docker this container needs persistent storage to keep /data in it, if we don't allocate to it, docker create an anonymous one and set it to /data on the container




## Contribute

All contributions are welcome:

- Read the issues, fork the project and do a Pull Request.
- Request a new topic creating a New issue with the enhancement tag.
- Find any kind of errors in the cheat sheet and create a New issue with the details or fork the project and do a Pull Request.



## License

This project is licensed under the Apache-2.0 License  - see the [LICENSE](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/LICENSE) file for details.

[@dwsclass](https://github.com/dwsclass) dws-ops-004-docker

[@dwsclass](https://github.com/dwsclass) dws-ops-005-docker

[@dwsclass](https://github.com/dwsclass) dws-ops-006-docker
