## Docker documentation notes

Main notes are taken from the Docker docs, additions from various sources

- <https://docs.docker.com/>
- <https://michael.bouvy.net/blog/en/2014/02/01/what-is-docker-and-how-to-use-it-lxc-openvz/>

Also, not detailed here is the very useful __[Docker Cheat Sheet](https://github.com/wsargent/docker-cheat-sheet)__

installation on Debian Jessie with latest versions (not default)

<https://docs.docker.com/engine/installation/linux/debian/#/debian-jessie-80-64-bit>

### Create, launch and control containers

Pull image foo (if needed) and make a container out of it, then launch it; if the local image is outdated, pull the new image from the docker hub:

```text
docker run foo

# or with a command appended (like /sbin/init?)
docker run foo /bin/echo « hello world »

# or explicitly daemonizing (returns the container ID in the output, and goes in the background)
docker run -d foo <command>

# run an interactive container (-t for pseudo tty, -i for interactive)
docker run -t -i ubuntu /bin/bash

# run a specific version (default is image_name:latest tag)
docker run -t -i ubuntu:12.04 /bin/bash

# run with random mapped ports for the exposed internal ports
docker run -d -P <image>

# run with specific mappings (bind to 0.0.0.0 by default, yuk) -multiple -p mentions are available
docker run -d -p localhost:8080:80 image

# give it a (non-random, as one is made up if none specified) name
docker run -ti --name my_foo_01 foo

# specify CWD in container (create it if not present)
docker run -w /home/foobar <image>

# delete a container (and its volumes with -v)
docker rm -v <CID>

# see files modified in the R/W layer
docker diff <CID>
```
#### Manage containers 

- once a container has been created (with pull/create/run) it gets a container ID, and a random name if none provided, visible in `docker ps`.
- several `docker run` invocations on the same image create as many different containers
 
```text
# get the logs
docker logs [-f] <container ID or name>

# list the containers
docker ps     # only running ones
docker ps -a  # all of them

# list the public-facing mapped port
docker port <container ID> <internal port num>

# control
docker start <container ID or name>
docker stop <container ID or name>

# get details about a container
docker inspect --format='' b5fc08fe5ffc | python -m json.tool
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <CID>

# see processes
docker top <CID>
```
        
### Dockerfile

#### Example

```text
FROM docker/whalesay:latest
 RUN apt-get -y update && apt-get install -y fortunes
 CMD /usr/games/fortune -a | cowsay
```

#### Guidelines

<https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/>

- build cache: `ADD` and `COPY` commands are checked for checksums of the files specified. Other commands like `RUN apt-get` are checked for the command line itself, not the resulting package versions etc. 
- use a `.dockerignore` file
- build in a clean root
- make containers ephemeral (no state)
- sort multi line args (package lists, etc) for better diffs -> no duplication of images

#### Instructions

- `FROM debian` (generally use debian as a base, tight and clean)
- `LABEL` for meta information, versions, provides, etc. multi-line possible
- `RUN` most often used with `apt` commands, chain them with && to prevent multiple images (1 image per instruction in the Dockerfile): `RUN apt-get update && apt-get install -y … && rm -rf /var/lib/apt/lists/*`
- `CMD` for launching the service in the container, like `CMD ["apache2","-DFOREGROUND"]` for a webserver, or `CMD ["perl", "-de1"]` for an interactive perl degugger
- `ENTRYPOINT` can point to the command to run instead of `CMD`, and the latter be used as the default options. Or be used in combination with a helper script that will trigger a shell `exec` so the daemon, for instance, becomes PID 1 and can receive signals sent to the container. See [Best practices: Entrypoint](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#/entrypoint)
- `ENV` for all parameters that may be needed in the container, for instance:

```text
ENV PG_MAJOR 9.3
ENV PG_VERSION 9.3.4
RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgress && …
ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH
```

- `ADD` and `COPY`: `COPY <file> <destination>`, `ADD` does more things, like tar extraction, etc. But `wget && tar -x && rm` is preferred (smaller images) 
- `VOLUME` for any mutable and/or user-serviceable parts of the image (databases, etc.)
- `USER` username to switch to (non-deterministic UIDs: `RUN groupadd -r postgres && useradd -r -g postgres postgres`)
- `WORKDIR` for building; use absolute paths preferrably
- `ONBUILD` post build hook; beware

<https://docs.docker.com/engine/reference/builder/>

### Build

pull the source image, apply commands, remove temporary image…

```text
docker build -t my-first-image .
```

Base instructions for building and committing custom images are here:
<https://docs.docker.com/engine/tutorials/dockerimages/>

### List locally available images

```text
docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
celery                        latest              b27a9776a5c2        13 days ago         213.2 MB
rabbitmq                      latest              e47df982f144        13 days ago         178.7 MB
sequenceiq/hadoop-docker      latest              5c3cc170c6bc        14 months ago       1.766 GB
kitematic/hello-world-nginx   latest              03b4557ad7b9        15 months ago       7.913 MB
sequenceiq/spark              latest              016b4fce9cd0        22 months ago       2.026 GB
```

### Tag and push an image, remove local versions, and pull the remote

```text
# tag ID is a UUID? no mention of random generation or checksum
docker tag 7d9495d03763 dmorel/my-first-image:latest
docker push dmorel/my-first-image

# 2 forms to remove a local image
docker rmi -f 7d9495d03763
docker rmi -f dmorel/my-first-image

# pull and run from remote
docker run dmorel/my-first-image

```

### create base images

Debian:

```text
$ sudo debootstrap raring raring > /dev/null
$ sudo tar -C raring -c . | docker import - raring
```

or for Centos: <https://github.com/docker/docker/blob/master/contrib/mkimage-yum.sh>

### ready-made images

<https://hub.docker.com/>

### Storage

- images are a pile of blobs identified by crypto hashes
- the writable layer on top (making an image a container) has a random ID
- major difference between image and container is the R/W layer on top
- containers can share the same image (shared mem?)
- sharing image layers speeds up start up time
- COW: changes go to container R/W layer
- container needs to write a lot? use a separate data volume
- data volumes can be shared between containers, or specific to a container
- storage driver is set for docker deamon at start time; several available [listed here](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/) with performance characteristics for each
- backing filesystem must be chosen to match storage driver and use case
- storage driver set in `DOCKER_OPTS` in `/etc/default/docker`

```text
# on my debian jessie machine
docker info
…
Storage Driver: devicemapper
Backing Filesystem: extfs
```

### Networking

[Docker networking documentation](https://docs.docker.com/engine/userguide/networking/#/understand-docker-container-networks)

- do not use `--link` anymore, use networks; docker has a built-in DNS to resolve container names within a network used with `--network`
- `docker network ls` for available networks, selectable on `run` with `—network`
    - bridge: default, binds docker0 IF to container's eth0, IP starting at 172.17.0.2, docker0 is 172.17.0.1
    - none: only lo0
    - host: same network config as the host
- better define other networks for real use: bridge, overlay or MACVLAN
    - `docker network create --driver bridge my_network`
    - `docker run --network=<NETWORK>`
    - **bridge** works on a single host, not accessible to outside; all containers on the host can see each other; ports mapping can be used to provide outside access to services
    - **overlay** to provide a multi-host network, on manager host in swarm mode, even without a K/V store for registration/discovery
    - **overlay** for multi-host, without swarm needs a K/V store (Consul, Etcd, ZooKeeper). Some steps needed for config, management ports, etc
    - docker daemon runs a DNS server, and forwards unanswered requests to an external resolver (for hosts outside of the bridge network)

### Data volumes

[Docker data volumes documentation](https://docs.docker.com/engine/tutorials/dockervolumes/)

- mounting a volume in the host is consistent with mount's behaviour: if some data exists on the path it will not overwrite but overlay it

- create a volume and mount it at /var/lib/webapp in the container

    `docker run --name web -v /var/lib/webapp training/webapp python app.py`
    
- create a named volume

    `docker run --name web -v foo:/var/lib/webapp training/webapp python app.py`
        
- find out where the data volume is

    `docker inspect web`

- mount a local directory on the host as a volume in the guest (NOTE: this can also be used to mount a *single file* if needed, not a full directory)

    `docker run --name web -v /src/webapp:/var/lib/webapp training/webapp python app.py`

- mount a volume readonly

    `docker run --name web -v foo:/var/lib/webapp:ro training/webapp python app.py`
    
- Volume plugins can be used to mount remote volumes, check the list of plugins
- Volume labels: `:z` for a shared volume in R/W mode, `:Z` is private unshared
- Mount data volumes from other containers: check `--volume-from`    

