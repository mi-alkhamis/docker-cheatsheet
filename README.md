# docker-cheatsheet

### intro

in this cheatsheet we review most used docker commands with their examples.



### Requirements

  You should have at least docker 20 :smile: !!!

### Commands

- Kill a container

  - first create a container 

  ```bash
  milad@docker:~$ docker run  -dit --name alpine alpine
  16afe7be3cb98004a4deb6411d470e04e1c0dea2a04b3047c3e239b7ef87accf
  ```
  - check running continer
  ```bash
  milad@docker:~$ docker ps
  CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
  16afe7be3cb9   alpine    "/bin/sh"   5 seconds ago   Up 3 seconds             alpine
  ```
  
  
   - Send KILL signal to container
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

    

- Create a image from running container

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

  - Create a new image (my-nginx) from alpine container

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

## 

## License

This project is licensed under the Apache-2.0 License  - see the [LICENSE](https://github.com/mi-alkhamis/script-runner/blob/main/LICENSE) file for details.