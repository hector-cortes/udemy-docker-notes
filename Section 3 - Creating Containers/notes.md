# Section 18: Check out our Docker install and config
## Docker commands
* docker version
  * get brief info about docker version and docker engine
* docker info
  * get detailed configuration info about docker version and docker engine
* docker
  * returns (incomplete) list of possible Docker commands

## Docker command format
### Old way (still works)
* docker \<command\> (options)
### New way
* docker \<command\> \<subcommand\> (options)
  * list of commands was growing large
  * new format introduced to help organize / structure command list

# Section 19: Starting Nginx web server
## Lecture Overview
* Containers vs Images
* Run/stop/remove containers
* Check container logs and processes

## Image vs Container
* image
  * application that we want to run
  * binaries, libraries, source code that make up the application
* containers
  * instance of that application running as a process

## Docker Commands
* docker container run --publish 80:80 nginx
  1. Looks for 'nginx' image locally, doesn't find it. Downlaods image 'nginx' from Docker Hub
  2. Starts a new container of that image
  3. Gives it a virtual IP on a private network inside of Docker Engine
      * --publish is the parameter that causes Docker to assign a virtual IP 
  3. Opens port 80 on the host IP (left number)
  4. Routes traffic to port 80 of the container IP (right number)
  5. Starts container by using the CMD in the image Dockerfile 

* docker container ls
  * Lists running containers

* docker container ls -a
  * Lists running containers, and previously running containers

* docker [COMMAND] --help
  * Shows all options for a command

* docker container stop [CONTAINER_ID]
  * Stops a running container

* docker container logs [CONTAINER_ID | CONTAINER_NAME]
  * Show logs for a specific container

* docker container rm
  * Removes a container

* docker top [CONTAINER_ID]
  * Lists running processes in a container

  # Section 21: Container vs VM
  ## Resources - Docker is not a VM
  * https://github.com/mikegcoleman/docker101/blob/master/Docker_eBook_Jan_2017.pdf
  * https://www.youtube.com/watch?v=sK5i-N34im8&feature=youtu.be&list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl
  * https://www.bretfisher.com/getting-a-shell-in-the-docker-for-windows-vm/
  * https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm/

  ## Resources - Docker Windows Containers
  * Windows Containers and Docker: 101
    * https://www.youtube.com/watch?v=066-9yw8-7c
  * Beyond \ - the path to Windows and Linux parity in Docker
    * https://www.youtube.com/watch?v=4ZY_4OeyJsw
  * Docker + Microsoft - Investing in the future of your applications
    * https://www.youtube.com/watch?v=QASAqcuuzgI
  
# Section 23: Assignment - Manage Multiple Containers
* Run nginx, mysql, httpd (apache) server
  * Run in detached mode, name them
  * Port requirements:
    * nginx - 80:80
    * mysql - 3306:3306
    * httpd - 8080:80
  * Environment variables:
    * mysql - MYSQL_RANDOM_ROOT_PASSWORD=yes
* Search container logs for random password
* Clean up with docker container stop, docker container rm
* Verify with docker container ls before and after cleanup
    
* Solution
```powershell
docker container run --name hector_nginx --detach --publish 80:80 nginx 
docker container run --name hector_httpd --detach --publish 8080:8080 httpd 
docker container run --name hector_mysql --detach --env MYSQL_RANDOM_ROOT_PASSWORD=yes --publish 3306:3306 mysql 
docker container logs hector_mysql | Select-String -Pattern "GENERATED ROOT PASSWORD"

docker container stop hector_nginx hector_httpd hector_mysql
docker container rm hector_nginx hector_httpd hector_mysql
docker container ps -a
```

* Notes
  * figure out why adding --name at the end of the command gave this error
    * starting container process caused "exec: \"--name\": executable file not found in $PATH": unknown

# Quiz 2
## Question 1: If you wanted to view running containers as well as containers that you have stopped and not removed, what command would you use?
* docker container ls -a
## Question 2: What does the -d flag do in a docker run command?
* It detaches the container to run in the background, and returns you to the shell prompt
## Question 3: Would the following two commands create a port conflict error with each other? 
* docker container run -p 80:80 -d nginx
* docker container run -p 8080:80 -d nginx
  * No. Correct, just because the containers are both listening on port 80 inside (the right number), there is no conflict because on the host they are published on 80, and 8080 separately (the left number)
## Question 4: I ran 'docker container run -p 80:80 nginx' and my command line is gone and everything looks frozen. Why?
* Because you didn't specify the -d flag to detach it in the background, and there aren't any Nginx logs yet

# Section 25: What's going on in Containers: CLI Process Monitoring
Multiple ways to see inside a container
* docker container top
  * process list in one container
* docker container inspect
  * details of one container config
  * shows metadata about container (startup, config, volumes, networking etc)
* docker container stats
  * performance stats for all containers

# Section 26: Getting a shell inside a container
* docker container run -it
  * starts a new container interactively
  * -t (tty, allocates a pseudo-TTY)
  * -i (interactive, keep STDIN open even if not attached)
* docker container exec -it
  * runs an additional command in an existing container
* docker container run -it --name proxy nginx bash
  * starts an nginx container in interactive mode, tells it start bash
```bash
docker container run -it --name proxy nginx bash
root@0a26b84c228a:/# ls -al
drwxr-xr-x   1 root root 4096 Mar 29 12:34 .
drwxr-xr-x   1 root root 4096 Mar 29 12:34 ..
-rwxr-xr-x   1 root root    0 Mar 29 12:34 .dockerenv
drwxr-xr-x   2 root root 4096 Feb 24 00:00 bin
drwxr-xr-x   2 root root 4096 Feb  1 17:09 boot
drwxr-xr-x   5 root root  360 Mar 29 12:34 dev
drwxr-xr-x   1 root root 4096 Mar 29 12:34 etc
drwxr-xr-x   2 root root 4096 Feb  1 17:09 home
drwxr-xr-x   1 root root 4096 Mar  4 17:31 lib
drwxr-xr-x   2 root root 4096 Feb 24 00:00 lib64
drwxr-xr-x   2 root root 4096 Feb 24 00:00 media
drwxr-xr-x   2 root root 4096 Feb 24 00:00 mnt
drwxr-xr-x   2 root root 4096 Feb 24 00:00 opt
dr-xr-xr-x 123 root root    0 Mar 29 12:34 proc
drwx------   2 root root 4096 Feb 24 00:00 root
drwxr-xr-x   3 root root 4096 Feb 24 00:00 run
drwxr-xr-x   2 root root 4096 Feb 24 00:00 sbin
drwxr-xr-x   2 root root 4096 Feb 24 00:00 srv
dr-xr-xr-x  13 root root    0 Mar 29 12:34 sys
drwxrwxrwt   1 root root 4096 Mar  4 17:31 tmp
drwxr-xr-x   1 root root 4096 Feb 24 00:00 usr
drwxr-xr-x   1 root root 4096 Feb 24 00:00 var
root@0a26b84c228a:/# exit
docker container -ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
docker container -ls -a
0a26b84c228a nginx "bash"                 35 seconds ago  Exited (0) 7 seconds ago proxy
0efae92b41ff nginx "nginx -g 'daemon of…" 4 minutes ago   Up 4 minutes             nginx
```

Notice the "COMMAND" section for each nginx image is different
* "nginx -g 'daemon of..." is the standard start-up command
* "bash" is the custom command we entered in. also notice the container stopped as soon
 as we exited the terminal. docker containers only run as long as the command that it ran on start up runs 

* docker container run -it --name ubuntu ubuntu
  * it's default command is "bash", so we don't have to run it
  * is a very lightweight version of ubuntu. many things missing that you'd expect
  from a "normal" ubuntu installation. additional software can always be added
  with apt package manager
* docker container start -ai ubuntu
  * command to attach to bash session is different for running container
  * -a (attach, Attach STDOUT / STDERR and forward signals)
  * -i (interactive, attach container's STDIN)
* docker container exec
  * runs additional process in running container

  # Section 27: Docker Networks: Concepts for private and public comms in containers
  ## Docker Networks Defaults
  * Each container connects to private virtual network "bridge" by default
  * Each virtual network routes through NAT firewall on host IP
  * All containers on a virtual network can talk to each other without -p
  * Best practice is to create a new virtual network for each application
  * Capabilities
    * Can make new virtual network
    * Attach containers to more than 1 virtual network (or none)
    * Skip virtual network and use host IP (--net=host)
    * Use different Docker network drivers to gain different abilities
  
  ## CLI
  * docker container run -p 80:80 --name webhost -d nginx
  * ```bash
    docker container port webhost
    80/tcp -> 0.0.0.0:80
    ```
  * docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost
    * allows you to view IP address of container

# Section 29: Docker Networks: CLI management of virtual networks
* docker network ls
  * lists all the networks that have been created
    * 'bridge' is the default Docker virtual network, which is NAT'ed behind host IP
    * 'host' attaches container directly to host IP. sacrifices security for performance
    * 'none' is equivalent of having eth0 interface on pc that is not attached to anything
* docker network inspect bridge
  * shows detailed information about the bridge network, such as what containers are attached, IP address info
* docker network create [OPTIONS] NETWORK
  * creates a new network
* docker container run -d --name nginx --network NETWORK nginx
  * creates a new nginx image, attaches it to newly created NETWORK
* docker network connect [NETWORK] [CONTAINER]
  * connects the specified container to the specified network
* docker network disconnect [NETWORK] [CONTAINER]
  * disconnects the specified container to the specified network

# Section 30: DNS and how containers find each other
## Overview
* Understand how DNS is the key to easy inter-container comms
* See how it works by default with custom networks
* Learn how to use --link to enable DNS on default bridge network
## Docker DNS
* Docker daemon has built-in DNS server that containers use by default
* Default DNS name is set to container name, but aliases can be used instead
* NOTE: Default 'bridge' network does not have DNS server enabled by default
  * can be done through --link (docker container create --link list: Adds link to another container, default [])

# Quiz 3
## Question 1: Which command would we use to get an overview look of information about a specific container like networks, IP address, mounts, and current status?
* docker container inspect
## Question 2: In order to connect to a shell inside a container, you should use docker ssh to get inside.
* false (should use docker exec -it [CONTAINER] sh)
## Question 3: If you wanted multiple containers to be able to communicate with each other on the same docker host, which network driver would you use?
* bridge

# Section 31: Assignment: Using containers for CLI testing
* Use different Linux distro containers to check curl cli tool version
* Use two different terminal windows to start bash in both centos:7 and ubuntu:14.04 using -it
* Learn docker container --rm option so you can save cleanup
* Ensure curl is installed and on latest version for that distro
  * ubuntu: apt-get update && apt-get install curl
  * centos: yum update curl
* check curl --version
* Solution
```powershell
docker container run --name centos -it centos bash -c "yum update curl; curl --version"
docker container rm centos
docker container run --name ubuntu -it ubuntu bash -c "apt-get update && apt-get install curl; curl --version"
docker container rm ubuntu
```

# Section 32: Assignment: DNS Round Robin Test
* Create a new virtual network
* Create two containers from elasticsearch image
* Use -network-alias search when creating the images to give them an additional DNS name to respond to
* Run alpine nslookup search with --net to see the two containers list for the same DNS name
* Run centos curl -s search:9200 --net multiple times until you see both "name" fields show
* solution
```powershell
docker network create elasticsearch_net
docker container run --detach --network-alias search --network elasticsearch_net --name elasticsearch_ctn_1 elasticsearch:2
docker container run --detach --network-alias search --network elasticsearch_net --name elasticsearch_ctn_2 elasticsearch:2
docker container run -it --rm --network elasticsearch_net alpine:3.10 /bin/sh -c "nslookup search;"
docker container run -it --rm --network elasticsearch_net centos bash -c "curl -s search:9200;"
```