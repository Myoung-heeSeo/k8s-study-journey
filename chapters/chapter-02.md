# Chapter 02: First steps with Docker and Kubernetes

## 📚 Summary

In Chapter 2, we delve into the hands-on world of Docker and Kubernetes, starting with the creation and management of Docker containers. We learn how to build a Docker image, run a container, and share images through a container registry. 
Transitioning to Kubernetes, we set up a single-node cluster locally to get a feel of how Kubernetes operates in a development environment. 
Key command-line skills are developed as we explore `kubectl`, the Kubernetes command-line client, learning how to interact with our cluster, deploy applications, and adjust configurations on the fly.
By the end of the chapter, we gain practical knowledge on deploying applications to Kubernetes, managing their lifecycle, and understanding how to scale applications horizontally to handle increased load, laying down a solid foundation for more advanced topics in the coming chapters.

## 📌 Key Points
### Creating, running and sharing a container image with Docker
- **Install Docker on Linux environment**
 http://docs.docker.com/engine/installation/
- **Download and run image with docker**
  
  Download busybox image with docker. Busybox is a single executable that combines many of the standard UNIX command-line tools, such as `echo`, `ls`, `gzip`.
  ```
  $ docker run busybox echo "Hello world"
  ```

  <img width="545" alt="스크린샷 2023-10-29 오후 11 53 57" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/8b13370f-ae5d-4f5b-95d5-6e30205ac29e">

  Docker supports having versions or variants of the same image under the same name. It can specified with tags next to image name. 
  ```
  $ docker run <image>:<tag>   # default tag: 'latest'
  ```

- **Creating a Dockerfile for the image**
   ```
   FROM node:7   # FROM: line defines the container image you’ll use as a starting point
   ADD app.js /app.js # ADD a b: add 'a' file to 'b' path
   ENTRYPOINT ["node", "app.js"]  # ENTRYPOINT: executed when somebody runs the image
   ```
- **Build Docker image**
  ```
  $ docker build -t kubia .  # docker build -t {image name} {path}
  ```
  
  <img width="544" alt="스크린샷 2023-11-01 오전 12 31 04" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/73d8a5f0-0dbe-40ff-8cf0-939232e1c1ec">
  
  Docker client and docker daemon don't need to be on the same machine. The contents of the whole directory are uploaded to the Docker daemon and the image is built there. 

  
  Images in Docker are made up of multiple layers, enhancing storage and transfer efficiency. While a Dockerfile might seem to produce one layer, each command in it actually creates a distinct layer, stacking them atop the base image layers.
  
  <img width="696" alt="스크린샷 2023-11-01 오전 12 38 59" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/d542ad51-8550-4433-bf8f-a515dcaad4a6">

  You can manually create a new image by starting a container from an existing image, executing commands inside it, then saving its final state as a new image after exiting.

- **Listing locally stored images**
  ```
  $ docker images
  REPOSITORY   TAG      IMAGE ID           CREATED             VIRTUAL SIZE
  kubia        latest   d30ecc7419e7       1 minute ago        637.1 MB
  ...
  ```

- **Running the container image**

  Run new container "kubia-container" from "kubia" base image with -d option (detached from console and running in the background).
  ```
  $ docker run --name kubia-container -p 8080:8080 -d kubia
  ```
  
 
- **Listing running containers**
  ```
  $ docker ps
  CONTAINER ID  IMAGE         COMMAND               CREATED        ...
  44d76963e8e1  kubia:latest  "/bin/sh -c 'node ap  6 minutes ago  ...
  
  ...  STATUS              PORTS                    NAMES
  ...  Up 6 minutes        0.0.0.0:8080->8080/tcp   kubia-container
  ```
  Listing containers including additional information
  ```
  $ docker inspect kubia-container
  ```
- **Exploring the inside of a running container** 

  Execute bash inside the running container 'kubia-container'. Below options need to make it run in general use.
  ```
  $ docker exec -it kubia-container bash
  ```
  - option '-i': makes sure STDIN is kept open. You need this for entering commands into the shell.
  - option '-t': allocates a pseudo terminal (TTY).
 
  Then, list processes from inside a container. You can only see the processes inside this container in the host OS.
  ```
  root@44d76963e8e1:/# ps aux
  USER  PID %CPU %MEM    VSZ   RSS TTY STAT START TIME COMMAND
  root    1  0.0  0.1 676380 16504 ?   Sl   12:31 0:00 node app.js
  root   10  0.0  0.0  20216  1924 ?   Ss   12:31 0:00 bash
  root   19  0.0  0.0  17492  1136 ?   R+   12:38 0:00 ps aux
  ```

  - If you’re using a Mac or Windows, you’ll need to log into the VM where the Docker daemon is running to see these processes.

  List all the processes containing 'app.js' using the command below in different terminal.

  ```
  $ ps aux | grep app.js
  USER  PID %CPU %MEM    VSZ   RSS TTY STAT START TIME COMMAND
  root  382  0.0  0.1 676380 16504 ?   Sl   12:31 0:00 node app.js
  ```
  PIDs in the 2 results above are different. The reason is the container is using its own PID Linux namespace and has a completely isloated process tree, with its own sequence of numbers.

  Likewise, each container has its own isolated filesystem.
  ```
  root@44d76963e8e1:/# ls /
  app.js  boot  etc   lib    media  opt   root  sbin  sys  usr
  bin     dev   home  lib64  mnt    proc  run   srv   tmp  var
  ```

- **Image Registry**

  To share the docker image to other computer, you need to push the image to an external image registry.
  - e.g. [Docker Hub](http://hub.docker.com) , Amazon ECR, Google Container Registry
  - To upload the image to Docker Hub, image should be re-tagged to start with your Docker Hub ID.
  - Container image tags are accummulated and each container image can have multiple tags.

## 🖥 Examples

```bash
# Stop processes inside the docker container and the container
$ docker stop kubia-container

# Check the running and paused containers -> kubia-container still exist since it's paused
$ docker ps -a

# Remove Docker container and all the contents inside
$ docker rm kubia-container
```
  
```bash
# Create an additional tag for the image to follow the Docker Hub's rules
$ docker tag kubia your_dockerhub_id/kubia

# List docker images
$ docker images | head
REPOSITORY        TAG      IMAGE ID        CREATED             VIRTUAL SIZE
luksa/kubia       latest   d30ecc7419e7    About an hour ago   654.5 MB
kubia             latest   d30ecc7419e7    About an hour ago   654.5 MB
docker.io/node    7.0      04c0ca2a8dad    2 days ago          654.5 MB
...

# Push the image to Docker Hub after login using docker login command
$ docker push your_dockerhub_id/kubia
```

```bash
# In any other machine, download the uploaded docker image
$ docker run -p 8080:8080 -d your_dockerhub_id/kubia
```
