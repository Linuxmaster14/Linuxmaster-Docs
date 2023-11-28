# Docker Basic

## Install Docker (Ubuntu)

### Uninstall Old Versions

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

### Set up the Repository

```bash
sudo apt-get update

sudo apt-get install ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Install Docker Engine

```bash
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Verify that Docker Engine is installed correctly by running the hello-world image.

```bash
sudo docker run hello-world
```

## Docker Images

```bash
docker images COMMAND
```

```bash
docker image --help

Usage:  docker image COMMAND

Manage images

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.
```

Ref: [https://docs.docker.com/engine/reference/commandline/image/](https://docs.docker.com/engine/reference/commandline/image/)

```bash
root@linuxmaster:~# docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB

root@linuxmaster:~# docker image pull nginx
Using default tag: latest
latest: Pulling from library/nginx
... 
Digest: sha256:b95a99feebf7797479e0c5eb5ec0bdfa5d9f504bc94da550c2f58e839ea6914f
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest

root@linuxmaster:~# docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    2b7d6430f78d   8 days ago      142MB
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB

root@linuxmaster:~# docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
...
Digest: sha256:bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad
Status: Downloaded newer image for alpine:latest
docker.io/library/alpine:latest

root@linuxmaster:~# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    2b7d6430f78d   8 days ago      142MB
alpine        latest    9c6f07244728   3 weeks ago     5.54MB
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB

root@linuxmaster:~# docker image rm nginx
Untagged: nginx:latest
Untagged: nginx@sha256:b95a99feebf7797479e0c5eb5ec0bdfa5d9f504bc94da550c2f58e839ea6914f
...
Deleted: sha256:6485bed636274e42b47028c43ad5f9c036dd7cf2b40194bd556ddad2a98eea63

root@linuxmaster:~# docker rmi alpine
Untagged: alpine:latest
Untagged: alpine@sha256:bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad...
Deleted: sha256:994393dc58e7931862558d06e46aa2bb17487044f670f310dffe1d24e4d1eec7

root@linuxmaster:~# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
```

### Build an image from a Dockerfile

```bash
docker image build --help

Usage:  docker image build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile

Options:
      --add-host list           Add a custom host-to-IP mapping (host:ip)
      --build-arg list          Set build-time variables
      --cache-from strings      Images to consider as cache sources
      --cgroup-parent string    Optional parent cgroup for the container
      --compress                Compress the build context using gzip
      --cpu-period int          Limit the CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int           Limit the CPU CFS (Completely Fair Scheduler) quota
  -c, --cpu-shares int          CPU shares (relative weight)
      --cpuset-cpus string      CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string      MEMs in which to allow execution (0-3, 0,1)
      --disable-content-trust   Skip image verification (default true)
  -f, --file string             Name of the Dockerfile (Default is 'PATH/Dockerfile')
      --force-rm                Always remove intermediate containers
      --iidfile string          Write the image ID to the file
      --isolation string        Container isolation technology
      --label list              Set metadata for an image
  -m, --memory bytes            Memory limit
      --memory-swap bytes       Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --network string          Set the networking mode for the RUN instructions during build
                                (default "default")
      --no-cache                Do not use cache when building the image
      --pull                    Always attempt to pull a newer version of the image
  -q, --quiet                   Suppress the build output and print image ID on success
      --rm                      Remove intermediate containers after a successful build (default true)
      --security-opt strings    Security options
      --shm-size bytes          Size of /dev/shm
  -t, --tag list                Name and optionally a tag in the 'name:tag' format
      --target string           Set the target build stage to build.
      --ulimit ulimit           Ulimit options (default [])
```

```bash
root@linuxmaster:~# docker image build -t firstapp:1.0 .
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM python:alpine
alpine: Pulling from library/python
213ec9aee27d: Pull complete 
6b2a141cd227: Pull complete 
a292fad6b52e: Pull complete 
4593e4e33a59: Pull complete 
9fc487f38654: Pull complete 
Digest: sha256:0c46c7f15ee201a2e2dc3579dbc302f989a20b1283e67f884941e071372eb2cc
Status: Downloaded newer image for python:alpine
 ---> ce4168535f30
Step 2/4 : WORKDIR /opt
 ---> Running in a0731f6162d6
Removing intermediate container a0731f6162d6
 ---> 0e888d12c01e
Step 3/4 : COPY ./app.py ./
 ---> 8152bff3fcb7
Step 4/4 : CMD python app.py
 ---> Running in 2e0cc2f2d3a9
Removing intermediate container 2e0cc2f2d3a9
 ---> 44f6ee3b8fe9
Successfully built 44f6ee3b8fe9
Successfully tagged firstapp:1.0

root@linuxmaster:~# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
firstapp      1.0       44f6ee3b8fe9   17 seconds ago   48.7MB
python        alpine    ce4168535f30   2 weeks ago      48.7MB
hello-world   latest    feb5d9fea6a5   11 months ago    13.3kB

root@linuxmaster:~# cat Dockerfile
FROM python:alpine
WORKDIR /opt
COPY ./app.py ./
CMD python app.py

root@linuxmaster:~# cat app.py
print('Hello World!')
```

## Docker Containers

```bash
docker container COMMAND
```

```bash
docker container --help

Usage:  docker container COMMAND

Manage containers

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  inspect     Display detailed information on one or more containers
  kill        Kill one or more running containers
  logs        Fetch the logs of a container
  ls          List containers
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  prune       Remove all stopped containers
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  run         Run a command in a new container
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker container COMMAND --help' for more information on a command.
```

Ref: [https://docs.docker.com/engine/reference/commandline/container/](https://docs.docker.com/engine/reference/commandline/container/)

```bash
root@linuxmaster:~# docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

root@linuxmaster:~# docker container ls -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
ddc30affeab2   hello-world   "/hello"   19 minutes ago   Exited (0) 19 minutes ago             youthful_matsumoto

root@linuxmaster:~# docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
ddc30affeab2   hello-world   "/hello"   20 minutes ago   Exited (0) 20 minutes ago             youthful_matsumoto

root@linuxmaster:~# docker build -t newapp -t newapp:1.0 .
Sending build context to Docker daemon  3.072kB
Step 1/6 : FROM python:alpine
 ---> ce4168535f30
Step 2/6 : RUN pip install flask
 ---> Running in 2355fe929f8a
Collecting flask
  Downloading Flask-2.2.2-py3-none-any.whl (101 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 101.5/101.5 kB 8.1 MB/s eta 0:00:00
Collecting Werkzeug>=2.2.2
  Downloading Werkzeug-2.2.2-py3-none-any.whl (232 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 232.7/232.7 kB 9.0 MB/s eta 0:00:00
Collecting itsdangerous>=2.0
  Downloading itsdangerous-2.1.2-py3-none-any.whl (15 kB)
Collecting Jinja2>=3.0
  Downloading Jinja2-3.1.2-py3-none-any.whl (133 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.1/133.1 kB 14.1 MB/s eta 0:00:00
Collecting click>=8.0
  Downloading click-8.1.3-py3-none-any.whl (96 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 96.6/96.6 kB 14.8 MB/s eta 0:00:00
Collecting MarkupSafe>=2.0
  Downloading MarkupSafe-2.1.1-cp310-cp310-musllinux_1_1_x86_64.whl (29 kB)
Installing collected packages: MarkupSafe, itsdangerous, click, Werkzeug, Jinja2, flask
Successfully installed Jinja2-3.1.2 MarkupSafe-2.1.1 Werkzeug-2.2.2 click-8.1.3 flask-2.2.2 itsdangerous-2.1.2
Removing intermediate container 2355fe929f8a
 ---> 5523d98df312
Step 3/6 : WORKDIR /opt
 ---> Running in 30fa670591f1
Removing intermediate container 30fa670591f1
 ---> b14b85b397bd
Step 4/6 : COPY ./app.py ./
 ---> 23e52c7226bf
Step 5/6 : EXPOSE 5000
 ---> Running in 36bfefc3e95b
Removing intermediate container 36bfefc3e95b
 ---> f35d9b43ba4d
Step 6/6 : CMD python ./app.py
 ---> Running in 39bee1319ef3
Removing intermediate container 39bee1319ef3
 ---> ae768a1f9832
Successfully built ae768a1f9832
Successfully tagged newapp:latest
Successfully tagged newapp:1.0

root@linuxmaster:~# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
newapp        1.0       ae768a1f9832   10 seconds ago   60.4MB
newapp        latest    ae768a1f9832   10 seconds ago   60.4MB
firstapp      1.0       44f6ee3b8fe9   8 minutes ago    48.7MB
python        alpine    ce4168535f30   2 weeks ago      48.7MB
hello-world   latest    feb5d9fea6a5   11 months ago    13.3kB

root@linuxmaster:~# docker container create --name newapp newapp
1aebfdb5997f7e0ecc4dfc6c2c5d9bd431533f2f109a287405bfde1b8bf43410

root@linuxmaster:~# docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                      PORTS     NAMES
1aebfdb5997f   newapp        "/bin/sh -c 'python …"   36 seconds ago   Created                               newapp
ddc30affeab2   hello-world   "/hello"                 24 minutes ago   Exited (0) 24 minutes ago             youthful_matsumoto

root@linuxmaster:~# docker container start newapp
newapp

root@linuxmaster:~# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS          PORTS      NAMES
1aebfdb5997f   newapp    "/bin/sh -c 'python …"   2 minutes ago   Up 49 seconds   5000/tcp   newapp

root@linuxmaster:~# docker stop newapp
newapp

root@linuxmaster:~# docker rm newapp
newapp
```

### Run Container in Attach mode

```bash
root@linuxmaster:~# docker run --name newapp -p 5000:5000 newapp
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 426-547-533
88.248.106.175 - - [01/Sep/2022 15:48:00] "GET / HTTP/1.1" 200 -
88.248.106.175 - - [01/Sep/2022 15:48:01] "GET /favicon.ico HTTP/1.1" 404 -
```

### Run Container in Detach mode

```bash
root@linuxmaster:~# docker rm newapp 
newapp

root@linuxmaster:~# docker run --name newapp -p 5000:5000 -d newapp
3f0328b58a6dfe22013256b3d22d028052aee0d8ab44c30446b01212d3cfd620

root@linuxmaster:~# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
3f0328b58a6d   newapp    "/bin/sh -c 'python …"   8 seconds ago   Up 7 seconds   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   newapp
```

### Run a command in a running container

```bash
root@linuxmaster:~# docker exec newapp top

Mem: 1370968K used, 610780K free, 1116K shrd, 35424K buff, 1034988K cached
CPU:   9% usr   0% sys   0% nic  90% idle   0% io   0% irq   0% sirq
Load average: 0.01 0.02 0.00 2/160 14
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    6     1 root     S    30220   2%   0   0% /usr/local/bin/python /opt/app.py
    1     0 root     S    28048   1%   0   0% python ./app.py
   10     0 root     R     1604   0%   0   0% top

root@linuxmaster:~# docker exec -it newapp sh
/opt #

root@linuxmaster:~# ls
app.py

root@linuxmaster:~# exit
```

* `--interactive or -i: Keep STDIN open even if not attached`
* `--tty or -t: Allocate a pseudo-TTY`

### Display detailed information on one or more containers

```bash
docker container inspect [OPTIONS] CONTAINER [CONTAINER...]
```

### List port mappings or a specific mapping for the container

```bash
docker container port CONTAINER [PRIVATE_PORT[/PROTO]]

# docker port newapp 
5000/tcp -> 0.0.0.0:5000
5000/tcp -> :::5000
```

```bash
root@linuxmaster:~# docker logs newapp 
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 120-117-977
88.248.106.175 - - [01/Sep/2022 15:52:36] "GET / HTTP/1.1" 200 -

root@linuxmaster:~# docker top newapp 
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                4792                4763                0                   15:52               ?                   00:00:00            python ./app.py
root                4823                4792                0                   15:52               ?                   00:00:01            /usr/local/bin/python /opt/app.py
root                4868                4763                0                   15:53               ?                   00:00:00            top

root@linuxmaster:~# docker stats newapp
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT    MEM %     NET I/O           BLOCK I/O   PIDS
3f0328b58a6d   newapp    0.21%     35.48MiB / 1.89GiB   1.83%     2.71kB / 1.02kB   0B / 0B     4
```

### Remove one or more containers

```bash
docker container rm [OPTIONS] CONTAINER [CONTAINER...]
```

### Remove all stopped containers

```bash
docker container prune [OPTIONS]
```

```bash
root@linuxmaster:~# docker stop newapp 
newapp
root@linuxmaster:~# docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                      PORTS     NAMES
3f0328b58a6d   newapp        "/bin/sh -c 'python …"   12 minutes ago   Exited (0) 6 seconds ago              newapp
ddc30affeab2   hello-world   "/hello"                 45 minutes ago   Exited (0) 45 minutes ago             youthful_matsumoto
root@linuxmaster:~# docker container prune 
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
3f0328b58a6dfe22013256b3d22d028052aee0d8ab44c30446b01212d3cfd620
ddc30affeab236d3023077f495603422c7b4a1b4ff781bbed696129c9c3fc3b1

Total reclaimed space: 111.4kB
```

```bash
root@linuxmaster:~# docker run firstapp:1.0
Hello World!

root@linuxmaster:~# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

root@linuxmaster:~# docker run -d firstapp:1.0
be92a6137535579486213512d99caa6bdd557a1f4a1841558d666de0264862c8

root@linuxmaster:~# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## Docker Networking
