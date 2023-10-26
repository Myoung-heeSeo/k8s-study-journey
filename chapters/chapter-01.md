# Chapter 01: Introduction to Kubernetes

Kubernetes, often referred to as K8s, is a powerful system developed by Google for managing containerized applications across a cluster of machines. This chapter serves as an introduction to its core concepts and components.

## 📚 Summary

In this chapter, we embark on our Kubernetes journey, starting with the reasons behind its inception and its rise to becoming the de facto standard for container orchestration. We explore the fundamental difference between virtual machines and containers and understand the unique challenges of managing containers at scale, which Kubernetes addresses.



## 📌 Key Points
### Why Kubernetes

- As applications broken down into microservices from monolithic applications and containerized applications grow, there's a need for orchestration.
- Kubernetes automates deploying, scaling, and operating application containers.

  <center><img width="457" alt="스크린샷 2023-10-27 오전 12 51 57" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/6d53337c-e61f-410b-95ea-2bbb5f6f60dc"></center>

- While monolithic application requires a small number of powerful servers, microservices are independent by each service and that makes them communicate through protocols.
- Even if microservices more difficult to debug and trace execution calls, it's better to scale independently.

  <center><img width="463" alt="스크린샷 2023-10-27 오전 12 51 30" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/2aef047b-0903-4a33-804c-a9484553ade7"></center>

### Containers vs. Virtual Machines

- Compared to VMs, containers are lightweight by sharing the host OS which make them faster and more efficient.

  <center><img width="637" alt="스크린샷 2023-10-27 오전 12 01 31" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/40cc2dcd-5aeb-4874-9dae-a1d763683d12"></center>

  <center><img width="491" alt="스크린샷 2023-10-27 오전 12 06 00" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/bba06612-77ae-49f9-b893-f09ee970efa7"></center>


- VMs offer full isolation but can be resource-heavy, while containers provide efficient, instant execution with shared kernel access, posing potential security concerns.

### How is it possible to make container isolation?
1. Linux Namespace: separate process to sees it's own personal view of the system (files, processes, network interfaces, hostname..)
2. Linux Control Groups (cgroups): limit amount of process's consumable resources (CPU, memory, network bandwidth..)

### Docker container platform

- Docker was the first container system that made containers easily portable across different machines. 
- A big difference between Docker-based container images and VM images is that container images are composed of layers. This means it can be shared and reused across multiple images.
- main concepts in Docker:
  - Images: packiging of application and its' environment.
  - Registries: repository to stores and share docker images.
  - Containers: regular Linux container created from a Docker-based container image.
    
  <center> <img width="465" alt="스크린샷 2023-10-27 오전 12 22 45" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/ae8fa7d9-712c-4ccf-ac22-38fe7516578a"> </center>

### About Kubernetes
- Origin of Kubernetes: After a decade of using their internal systems Borg and Omega, Google introduced Kubernetes in 2014.
- Kubernetes system is composed of a master node and any number of worker nodes.
  <center> <img width="475" alt="스크린샷 2023-10-27 오전 12 33 23" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/9af0c35c-f940-4b10-a05b-b1f24c88b71f"> </center>
  
  - **Master node:** hosts the Kubernetes Control Plane to controls and manages the whole Kubernetes system.
  - **Worker nodes:** run the actual applications that are deploy.

    <center><img width="459" alt="스크린샷 2023-10-27 오전 12 36 39" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/759cb028-8cd3-425d-b695-ac657a0d75e5"></center>

  - **Components of Controle Plane**:
    - **Kubernetes API Server:** communicate with user and Control Plane components.
    - **Scheduler:** schedules apps by assigning a worker node to each component of your application.
    - **Controller Manager:** perform cluster-level functions (e.g. replicate components, keep track of worker nodes, handle node failures)
    - **etcd:** reliable distributed data store to stores the cluster configuration persistently.
   
  - **Compoenets of worker nodes:**
    - **Docker, rkt, another container runtime:** runs containers
    - **Kubelet:** communicate with API server and manages containers on its node
    - **Kubernetes Service Proxy (kube-proxy):** load-balances network traffic between application components

- The app descriptor lists four containers, grouped into three sets (pods). The first two pods each contain only a single container, whereas the last one contains two.
  <img width="451" alt="스크린샷 2023-10-27 오전 12 46 37" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/c7dd735b-25d7-4728-b5b9-96db7a7fc0c7">

- Once the application is running, Kubernetes continuously makes sure that the deployed state of the application always matches the description you provided. It can automatically keep adjusting the number based on real-time metrics.
---

