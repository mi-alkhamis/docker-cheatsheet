[![](https://img.shields.io/badge/dwclass-dev--004--git-brightgreen?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/README.md)
[![](https://img.shields.io/badge/dwclass-dev--005--git-brightgreen?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/README.md)
[![GitHub license](https://img.shields.io/github/license/mi-alkhamis/docker-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/LICENSE)
[![GitHub forks](https://img.shields.io/github/forks/mi-alkhamis/docker-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/network)
[![GitHub issues](https://img.shields.io/github/issues/mi-alkhamis/docker-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-cheatsheet/issues)

# Docker Cheatsheet

### Intro

in this cheat sheet, we review the most used docker commands with their examples.



### Requirements

  You should have at least docker 20 :smile: !!!



### Commands

- Run Nginx container and show Docker version and exposed ports

  ```bash
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
  milad@docker:~$ 
  ```

  

- Make an new image from Nginx

  ```bash
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

  ```bash
  milad@docker:~$ docker run --rm -dit -p 8081:80 --name nginx nginx
  c836cd3ae2f72d68ea66192f01073608154bd065b2b5a16be93cb3861c0bd61c
  
  ```

  

- Show processes of conatainer

  ```bash
  milad@docker:~$ docker top nginx
  CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O         PIDS
  c836cd3ae2f7   nginx     0.00%     6.836MiB / 980.6MiB   0.70%     2.35kB / 1.84kB   13.9MB / 16.4kB   5
  
  ```

  Or

  ```bash
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

- - first, create a container 

  ```bash
milad@docker:~$ docker run  -dit --name alpine alpine
  16afe7be3cb98004a4deb6411d470e04e1c0dea2a04b3047c3e239b7ef87accf
  ```
  - check the running container
  ```bash
  milad@docker:~$ docker ps
  CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
  16afe7be3cb9   alpine    "/bin/sh"   5 seconds ago   Up 3 seconds             alpine
  ```
  
  
   - Send KILL signal to the container
    ```bash
    milad@docker:~$ docker kill alpine 
    alpine
    ```
   - Check container 
    ```bash
    milad@docker:~$ docker ps -a
    CONTAINER ID   IMAGE      COMMAND     CREATED          STATUS                       PORTS     NAMES
    16afe7be3cb9   alpine     "/bin/sh"   20 seconds ago   Exited (137) 6 seconds ago             alpine 
    
    ```
  
    

- Create an image from the running container

  - Run a container of alpine

    ```bash
    milad@docker:~$ docker run --rm -dit --name alpine alpine 
    c39b54f5050b09dc5f56a7a3ef4141bf764fdd4c3cea3f911483fbd6d97ee4c6
    ```

  - Install NGINX 

    ```bash
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
    / # exit
    ```

  - Create a new image (my-nginx) from the container

    ```bash
    milad@docker:~$ docker commit alpine  my-nginx
    sha256:c044d636a3b1005a79ba56e20b4f92e4f93fd6f254aa34820351dbe10ffc4eb4
    ```

  - List Images

  ```bash
  milad@docker:~$ docker images
  REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
  my-nginx           latest    c044d636a3b1   5 seconds ago   9.18MB
  alpine-nginx       0.1       c623dc02116a   27 hours ago    9.18MB
  ```

- pause a container

  ```bash
  milad@docker:~$ docker container pause alpine 
  alpine
  milad@docker:~$ docker ps
  CONTAINER ID   IMAGE     COMMAND     CREATED          STATUS                   PORTS     NAMES
  d915171187f6   alpine    "/bin/sh"   23 seconds ago   Up 20 seconds (Paused)             alpine
  ```
  
- get Id of running container

  - Get a Container ID with jq command

  ```bash
  milad@docker:~$ docker container inspect  redis |jq .[0].Id
  "fa528402d91bb18b2b7fe2e3ac4180fb2383469d840b30f87b57b976747fedca"
  ```

- Limit container resources

  ```
  milad@docker:~$ docker run -d --rm --cpus 0.5 --cpuset-cpus 0,2 --memory 512mb --memory-swap 768mb --name nginx-cpu nginx
  
  ```

- use environments variables

  ```bash
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

  ```bash
  milad@docker:~$ docker run --rm -dit -v /tmp:/data -w /data  --name mount-volume alpine
  ee16d9b91f641841da3bd8f88dec3180d7dde8a424a8ed2a2d538735ada54a7f
  milad@docker:~$ docker exec -it mount-volume sh
  /data # ls
  snap.lxd                 
  systemd-private-8uEIhg   
  systemd-private-8
  /data # 
  
  ```

  

## Contribute

All contributions are welcome:

- Read the issues, fork the project and do a Pull Request.
- Request a new topic creating a New issue with the enhancement tag.
- Find any kind of errors in the cheat sheet and create a New issue with the details or fork the project and do a Pull Request.



## License

This project is licensed under the Apache-2.0 License  - see the [LICENSE](https://github.com/mi-alkhamis/docker-cheatsheet/blob/main/LICENSE) file for details.

[@dwsclass](https://github.com/dwsclass) dws-ops-004-docker

[@dwsclass](https://github.com/dwsclass) dws-ops-005-docker
