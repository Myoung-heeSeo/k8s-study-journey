# Chapter 01: First steps with Docker and Kubernetes

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
  $ docker run <iamge>:<tag>   # default tag: 'latest'
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
  ```
  $ docker run --name kubia-container -p 8080:8080 -d kubia
  ```
  - Run new container "kubia-container" from "kubia" base image with -d option (detached from console and running in the background).
 
- **Listing running containers**
  ```
  $ docker ps
  CONTAINER ID  IMAGE         COMMAND               CREATED        ...
  44d76963e8e1  kubia:latest  "/bin/sh -c 'node ap  6 minutes ago  ...
  
  ...  STATUS              PORTS                    NAMES
  ...  Up 6 minutes        0.0.0.0:8080->8080/tcp   kubia-container
  ```
  Listing with additional information
  ```
  $ docker inspect kubia-container
  ```
