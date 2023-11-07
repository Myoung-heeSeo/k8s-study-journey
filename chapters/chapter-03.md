# Chapter 03: Pods: Running Containers in Kubernetes

## 📚 Summary
-  Introuduce the Pods in Kubernetes and create, execute and pause the Pods.
## 📌 Key Points
### Introducing pods
Pod is a co-located group of containers (1 or more) running on a single worker node which represents the basic building blocks in Kubernetes.

  <img width="543" alt="스크린샷 2023-11-07 오후 10 27 14" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/2bb7867b-8a21-423b-8bec-7c6274170543">

- Containers are designed to run only a single process per container unless it aim to create it's child processes. It's easier for managing their logs and retry on fail mechanism. **Pods are the higher-level construct to bind containers together and manage them as a single unit.**

- Pod run closely related processes together and provide them with the same environment even if they're in different container isolately.
- Kubernetes provide isolated but resource sharing environment by configuring Docker. Each container share the same set of Linux namespaces in the same pod. 
- Containers in a same pod share the same hostname (under the same UTS namespaces), network interfaces, IPC namespace and PID namespace (not enabled as a default)
- But when it comes to the filesystem, since it comes from the container image, the filesystem of each container is fully isolated from other containers.
- Since containers in the same pod share the same IP address and port space, proccesses running in the containers of the same pod need to care not to bind to the same port numbers. If not, they'll run into port conflicts.
- Pods use the flat network, so each pod can access to other pod using IP address regardless of which worker node they're at, no NAT (Network Address Translations) gateways needed. It's like computers on a local area network (LAN).

  <img width="560" alt="스크린샷 2023-11-07 오후 11 21 43" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/aa9a3e22-4779-403f-b00d-5cef8d5a445c">

#### Advantages of Spliting into multiple pods
- Utilize the resources in different worker nodes if each container has different role
- To scale out easily

<img width="556" alt="스크린샷 2023-11-08 오전 12 00 17" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/0ba4ef77-2a5d-4c47-bb02-069d18c790d7">

#### Use multiple containers in a pod
If there's main process and one or more related complementary processes, it's good to put those in the same pod.
e.g. log rotators and collectors, data processors, communication adapters

<img width="551" alt="스크린샷 2023-11-07 오후 11 53 57" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/a3a7fc69-5fa4-49e8-9e9f-357cbadb7339">

- Main container: web server that serves files from a certain file directory
- Supporting container 1: download and store contents into web-server's dir
- Volume: volume that you mount into both containers (address in chapter 6)


  

