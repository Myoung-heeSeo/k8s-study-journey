# Chapter 02: First steps with Docker and Kubernetes

## 📚 Summary

In Chapter 2, we delve into the hands-on world of Docker and Kubernetes, starting with the creation and management of Docker containers. We learn how to build a Docker image, run a container, and share images through a container registry. 
Transitioning to Kubernetes, we set up a single-node cluster locally to get a feel of how Kubernetes operates in a development environment. 
Key command-line skills are developed as we explore `kubectl`, the Kubernetes command-line client, learning how to interact with our cluster, deploy applications, and adjust configurations on the fly.
By the end of the chapter, we gain practical knowledge on deploying applications to Kubernetes, managing their lifecycle, and understanding how to scale applications horizontally to handle increased load, laying down a solid foundation for more advanced topics in the coming chapters.

## 📌 Key Points
### Creating, running and sharing a container image with Docker
- **Install Docker on Linux environment**

  - http://docs.docker.com/engine/installation/

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

### Setting up a Kubernetes cluster

- **There's several ways to set up a Kubernetes cluster:**
  - Set up by yourself
    - on your local development machine
    - on your own organization's cluster of machines
    - on cloud providers providing virtual machines
      e.g. Google Compute Engine, Amazon EC2, Microsoft Azure
  - Set up by using a managed Kubernetes cluster
    e.g. GKE(Google Kubernetes Engine), AWS EKS(Elastic Kubernetes Service)

- **Tools**
  - **Minikube**: a tool to sets up a single-node cluster to test Kubernetes and developing apps locally.
  - **kubectl**: CLI client to work with Kubernetes. kubectl get command can list all kinds of Kubernetes objects.

- **Interaction with three-node K8s cluster**

  <img width="544" alt="스크린샷 2023-11-03 오전 12 14 57" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/eb7d58c2-a2f3-47fa-aad5-2dd73ea4a3eb">

  - Each node runs Docker, Kubelet and the kube-proxy
  - Engineer interact with the cluster through the kubectl CLI
  - kubectl issues REST requests to the Kubernetes API server running on the master node

### Running simple app on Kubernetes

The simpletes way to deploy your app without JSON or YAML file is to use the `kubectl run` command.
```bash
$ kubectl run kubia --image=luksa/kubia --port=8080 --generator=run/v1
replicationcontroller "kubia" created
```

### Pod

- A group of one or more related containers that run together on the same worker node and in the same Linux namespace(s).
- Each pod is like a separate logical machine with its own IP, hostname, processes, and so on.

  <center><img width="535" alt="스크린샷 2023-11-03 오전 12 36 36" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/7eae337b-ca23-4826-a25a-c480348a1e17"></center>


  <img width="546" alt="스크린샷 2023-11-03 오전 12 41 00" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/e4984552-02cf-4a6e-bcb7-a0071493695c">

#### Scheduling
In Kubernetes, it means assigning the pod to a node. Pod is run immediately once it's assigned.

#### Service
- Each pod has it's own internal IP address, so to make the pod accessible from the outside, create a LoadBalancer type service and the external LoadBalancer will be created and you can connect to the pod through the load balancer's public IP.
- LoadBalancer type service not supported in Minikube. You can get accessible IP and port using kubia-http service.

  <img width="524" alt="스크린샷 2023-11-04 오전 12 48 24" src="https://github.com/Myoung-heeSeo/k8s-study-journey/assets/43746377/77b6e775-c38a-4b99-9d69-fa55fd787fa2">

#### Why you need a service
Pods are ephemeral which means it may disappear at any time if someone deleted the pod, or if the pod was evicted from an otherwise healthy node. If any of those occurs, a missing pod is replaced with a new one by the Replication-Controller with a new IP address. By using services, new pods will have a same static IP addresses as before. 

### Principle in Kubernetes
You're only declaratively changing the desired state of the system and letting Kubernetes examine the current actual state and reconcile it with the desired state.

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

```bash
# Install Minikube
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/
➥  v0.23.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube
➥  /usr/local/bin/

# Start minikube virtual machine
$ minikube start
Starting local Kubernetes cluster...
Starting VM...
SSH-ing files into VM...
...
Kubectl is now configured to use the cluster.

# Log into the minikube VM  to explore
$ minikube ssh

# Install kubectl for OSX
$ curl -LO https://storage.googleapis.com/kubernetes-release/release
➥  /$(curl -s https://storage.googleapis.com/kubernetes-release/release
➥  /stable.txt)/bin/darwin/amd64/kubectl
➥  && chmod +x kubectl
➥  && sudo mv kubectl /usr/local/bin/

# Display cluster information when the cluster is up
$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/proxy/...
kubernetes-dashboard is running at https://192.168.99.100:8443/api/v1/...
```

```bash
# List cluster nodes to check if the clusster is up
$ kubectl get nodes
NAME                      STATUS  AGE  VERSION
gke-kubia-85f6-node-0rrx  Ready   1m    v1.5.3
gke-kubia-85f6-node-heo1  Ready   1m    v1.5.3
gke-kubia-85f6-node-vs9f  Ready   1m    v1.5.3

# List additional details of node
$ kubectl describe node {node name}

# Don't need to specify the object's name if there's only one existing object
$ kubectl get node
$ kubectl describe node

# To set up alias, add below line to ~/.bashrc file
$ alias kc=kubectl
```

```bash
# List pods (worker node download the container image before run the container)
$ kubectl get pods
NAME          READY     STATUS    RESTARTS   AGE
kubia-4jfyf   1/1       Pending   0          5m
```

```bash
# Create the Loadbalancer type service (rc = replicationcontroller)
$ kubectl expose rc kubia --type=LoadBalancer --name kubia-http
service "kubia-http" exposed

# List services. Since it takes time for the load balancer to be created, it doesn't have an external IP address yet.
$ kubectl get services
NAME         CLUSTER-IP     EXTERNAL-IP   PORT(S)         AGE
kubernetes   10.3.240.1     <none>        443/TCP         34m
kubia-http   10.3.246.185   <pending>     8080:31348/TCP  4s

# List services again
$ kubectl get svc
NAME         CLUSTER-IP     EXTERNAL-IP   PORT(S)         AGE
kubernetes   10.3.240.1     <none>        443/TCP         35m
kubia-http   10.3.246.185   104.155.74.57 8080:31348/TCP  1m

# Send requests to the pod through external IP and port
$ curl 104.155.74.57:8080
You've hit kubia-4jfyf

# Get accessible IP and port in minikube
$ minikube service kubia-http
```

```bash
# List replicationcontroller
# DESIRED: number of pod replicas you want t he RC to keep
# CURRENT: actual number of pods currently running
$ kubectl get replicationcontrollers
NAME        DESIRED    CURRENT   AGE
kubia       1          1         17m

# List all the resources types
$ kubectl get

# Increasing the desired replica count
$ kubectl scale rc kubia --replicas=3
replicationcontroller "kubia" scaled

# Get rc result
$ kubectl get rc
NAME        DESIRED    CURRENT   READY   AGE
kubia       3          3         2       17m
```

```
# List pod with node info
$ kubectl get pods -o wide
NAME          READY   STATUS    RESTARTS   AGE   IP         NODE

# Show details of a pod
$ kubectl describe pod kubia-hczji
Name:        kubia-hczji
Namespace:   default
Node:        gke-kubia-85f6-node-vs9f/10.132.0.3        ❶
Start Time:  Fri, 29 Apr 2016 14:12:33 +0200
Labels:      run=kubia
Status:      Running
IP:          10.1.0.2
Controllers: ReplicationController/kubia
Containers:  ...
Conditions:
  Type       Status
  Ready      True
Volumes: ...
Events: ...
kubia-hczji   1/1     Running   0          7s    10.1.0.2   gke-kubia-85...

# Open dashboard when running Kubernetes using minikube
$ minikube dashboard
```
```
