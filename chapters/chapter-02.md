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
  $ docker run <iamge>:<tag>  -- (default tag: 'latest')
  ```
