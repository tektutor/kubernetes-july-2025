# Day 2

## Info - Container Orchestration Platform Overview
<pre>
- in real-world, i.e in Software industry we almost never try to manage container ourselve
- we make use of Container Orchestration Platforms
- We can only deploy containerized applications into Container Orchestration Platforms
- it has inbuilt 
  - monitoring tools to check the health, readiness & liviness of your application
  - it can repair your applications in case your application stops responding
  - it can help you scale up/down your application instances depending user traffic to your application
  - you can upgrade your already live application from one version to other without any download using rolling update feature
  - you could also rollback to older stable version in case you found the new version with faults
  - you can expose your application for internal only use or to external world using services
  - also it provides an environment with necessary tools to make your application Highly available (HA)
- examples
  - Docker SWARM
  - Kubernetes
  - Openshift
</pre>

## Info - Docker SWARM
<pre>
- it is Docker's native Container Orchestration Platform
- it generally works as a group of servers(cluster)
- some of those servers will act as master and rest of them will act as worker nodes
- it is lightweight, ideal for Developer/QA/learning purpose
- can be easily installed on your laptop with basic system configuration
- it is not production grade
- it only supports docker container application workloads
- it is free tool from Docker Inc
</pre>

## Info - Kubernetes
<pre>
- opensource container orchestration platform from Google
- it is production grade and robust
- supports only command-line interface
- works as a cluster ( group of servers )
- the servers can be 
  - Virtual Machines running locally or on private/public/hybrid clouds
  - Physical server on your data center
  - any cloud machines
  - each of those server(aka nodes in Kubernetes) must be installed with some Linux OS
  - each of those servers should also have some container runtime installed
  - each of those servers will have kubeadm, kubectl and kubelet tools installed
  - the nodes are 2 types
    1. Master and
    2. Worker
  - Master Node
    - a special set of components called Control Plane Components will be running in these nodes
    - Control Plane
      - API Server
      - etc key/value distributed database
      - Scheduler
      - Controller Managers - group of controllers
    - Control Plane components only runs in the master node
    - generally the master node will not allow deploying user applications
    - if required can be configured to allow deploying user applications 
  - Worker Node
    - this is where user containerized applicaitons will be running
  - tools
    - kubeadm
      - is a Kubernetes administration tool
      - it is used to bootstrap(setup) master nodes
      - it is also used to add additional master/worker nodes to the Kubernetes cluster
      - it is also useful incase you wish to remove some nodes from the Kubernetes cluster
    - kubelet
      - is service that runs in all Kubernetes Master & Worker Nodes
      - we will not directly interact with it
      - this component interacts with the Container Runtime
      - this component will download required container image, create and manage Pods, etc., 
    - kubectl
      - client tool that helps us interact with Kubernetes cluster
  
</pre>

## Demo - Install minikube kubernetes cluster
```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
minikube start
```

Expected 
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/5ff35a42-b332-43cb-9dc5-43bc404c001e" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/0b0efb46-ea36-49be-8f57-7c8ed956465a" />

## Info - Troubleshooting minikube start
```
minikube start --driver=docker --profile=<your-unique-name>
minikube profile list
# Switch to your profile
minikube profile <your-profile-name>
kubectl --context=jegan get nodes
kubectl config use-context jegan
```

## Info - Kubernetes Resources/Objects
<pre>
- Pod
- ReplicaSet
- Deployment
- DaemonSet
- StatefulSet
- Job
- CronJob
- Service
</pre>

#### Pod
<pre>
- is a group of related containers
- all containers that are part of a Pod shares the same IP Address
- all containers that are part of a Pod shares the same Port Range ( 0 to 65535 Ports )
- every container has its own filesystem though they are part of a Pod
- Pod is a logical grouping of containers
- though technically we can run many containers/applications, ideally only one main application should be ther per Pod
- the smallest unit that can be deployed into Kubernetes
- every Pod has one secret infra-container called pause container
- the job of pause container to provide network to the application container with the Pod
- every Pod will have one pause-container
- every Pod should have just one main application
</pre>

## Info - ReplicaSet

## Info - Deployment
<pre>
- any stateless application can be deployed as deployment
- Deployment is a resource/object supported by Kubernetes
- Deployment is a YAML definition that is stored inside etcd database
- Deployment is taken input by Deployment Controller, Deployment Controller manages Deployment
- Deployment represents your stateless application running inside the k8s cluster

</pre>

## Lab - Creating a Pod using Docker
Create a pause container to support network for nginx web server container
```
docker run -d --name nginx-pause --hostname nginx registry.k8s.io/pause:latest
docker ps
```

Let's create the nginx web server container and connect it with the nginx-pause container's network
```
docker run -d --name nginx --network=container:nginx-pause nginx:latest
docker ps
```

Let's find the IP address of the nginx-pause container
```
docker inspect nginx-pause | grep IPA
```

Let's get inside the nginx container shell
```
docker exec -it nginx /bin/sh
hostname
hostname -i
exit
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/6a7cf01a-a916-430c-b10e-2ac095bb44e0" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/4eb85229-0d73-43be-9bf3-a59ff1804890" />

## Lab - Listing the nodes in a Kubernetes cluster
```
kubectl get nodes 
kubectl get node
kubectl describe node/minikube
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/6c3dfaca-bfb3-42ce-813b-900e6b9d21fb" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/bee7211c-7c5b-42cb-b029-5eeab642cd68" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/2f24600e-faa9-49c4-9290-3105635b2a98" />

## Lab - Listing the control plane components
```
kubectl get pods -n kube-system
kubectl get pod -n kube-system
kubectl get po -n kube-system

```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/f140d4b2-51ba-4a1c-803b-ac7dc60d5197" />

You could also list all pods from all namespaces as shown below
```
kubectl get pods --all-namspaces
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/0ab4d621-b440-4628-ae6e-a3eba88310a1" />

## Lab - Listing the namespaces in your Kubernetes cluster
```
kuebctl get namespaces
```

<img width="1171" height="475" alt="image" src="https://github.com/user-attachments/assets/02fe0bf9-24fa-4b09-ae69-9aaa53d1a9f8" />

## Lab - Let's create a namespace to deploy applications
Replace 'jegan' with your name
```
kubectl create namespace jegan
kubectl get namespaces
kubectl get namespace
kubectl get ns
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/30fef731-86bd-42a8-a6c1-7c49f92c3d6b" />

## Lab - Deploying your first application into Kubernetes
In the below command, replace jegan with your namespace
```
kubectl create deploy nginx --image=nginx:latest --replicas=3 -n jegan
```
<pre>
- In the above command, we have created a deployment named nginx in the namespace jegan
- replicas, indicates the number of Pods instance you wish created within the deployment nginx
- image=nginx:latest tells, that the Pods should be created using nginx:latest image from docker hub registry
</pre>

List the deployments
```
kubectl get deployments -n jegan
kubectl get deployment -n jegan
kubectl get deploy -n jegan
```

List the replicasets
```
kubectl get replicasets -n jegan
kubectl get replicaset -n jegan
kubectl get rs -n jegan
```

List the pods
```
kubectl get pods -n jegan
kubectl get pod -n jegan
kubectl get po -n jegan
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/f4d02fc7-901a-4e09-be60-89e3674c05e9" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/02e359ef-17c8-441e-979d-5f30a18a33c1" />

Checking events related to deployment
```
kubectl describe deploy/nginx -n jegan
```

Checking events related to replicaset
```
kubectl describe rs/nginx-nginx-54c98b4f84-zv72k
```

Checking events related to pod
```
kubectl describe pod/nginx-54c98b4f84-zv72k
```

In general, you wish to see all events
```
kubectl get events -n jegan
```

## Info - What happens when we issue the below command inside Kubernetes cluster
```
kubectl create deploy nginx --image=nginx:latest --replicas=3 -n jegan

```

<pre>
- kubectl is a client tools, that helps us interacting with Kubernetes cluster
- kubectl is REST API client tool
- kubectl makes a REST API call to API server when we issue the above command
- API Server which runs in the master node(s), receives the request, it then creates a Deployment record(yaml) in etcd database
- API Server then sends a broadcasting event saying new Deployment created
- Deployment Controller which is part of Controller Managers Control Plane components.  It runs in every master node.
- Deployment Controller receives the new Deployment created event sent by API server, it then sends a REST API call requesting API Server to create a replicaset for nginx deployment
- API Server receives the request, it then creates a ReplicaSet database entry(record) in etcd database
- API Server then sends a broadcasting event saying new ReplicaSet created
- ReplicaSet Controller is part of Controller Managers Control Plane Components.  ReplicaSet controller receives this event, it then understand 3 Pods must be created.
- ReplicaSet Controller makes a REST call to API Server, requesting it to create 3 Pods
- API Server creates 3 Pod(YAML) records in the etcd database
- API Server will send broadcasting events saying new Pod created, there will one event broadcasted for each Pod
- Scheduler receives the new Pod created event, it then identifies a healthy node where the new Pod can be scheduled
- Scheduler then sends its scheduling recommendations to the API Server via REST call
- API Server receives the scheduling recommendations from Scheduler, it retrieves the existing Pod records from etcd database, it then updates the scheduling information on the respective Pod database entry
- API Server sends broadcasting event saying Pod scheduled to so and so node
- kubelet container agent running on the respective node receives the event, it then pulls the respective container image, it then creates the Pod containers 
- kubelet then monitors the Pod containers, it keeps reporting the status of all the containers managed by kubelet to the API Server periodically like heart-beat event via REST call
</pre>
![deployment](deployment.png)

## Lab - Finding IP Address of Node and Pods
```
kubectl get pods -o wide
kuebctl get noes -o wide
```
<img width="1942" height="409" alt="image" src="https://github.com/user-attachments/assets/08a337b9-0029-42e5-8e8d-6491c23bff2d" />

As the Pod IP is private IP, we won't be able to ping them from our local machine, however we can get inside the minikube node and ping them
```
minikube ssh
curl http://10.244.0.6:80
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/1a23957f-8102-4f24-9c2c-90502f9adf6a" />

## Lab - Using port-forwarding for testing purpose ( Not to be used in production )
```
kubectl get pods -n jegan
kubectl port-forward pod/nginx-54c98b4f84-kgztm -n jegan 8080:80
```

From another terminal window, you try accessing the web page
```
curl http://localhost:8080
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4f391947-24cc-4dfd-a45d-a0edb458921c" />

To stop the port-forward, press Ctrl+C on the terminal where ran did port-forward command.

## Lab - Setting the context to your namespace
```
kubectl config set-context --current --namespace=jegan
kubectl get deploy,rs,po
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/6bb7db56-fca5-45e6-a758-2c93a26518d3" />

## Lab - Editing deploy, replicaset, pods

```
kubectl config set-context --current --namespace=jegan
kubectl edit deploy/nginx
kubectl edit rs/nginx-nginx-54c98b4f84
kubectl edit pod/nginx-54c98b4f84-kgztm
```

Find the namespace your context is pointing to
```
kubectl config view --minify | grep namespace
```

Edit the deploy 
```
kubectl edit deploy/nginx
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b3e64d64-6efd-4bb7-a1c5-d0de3307a0f7" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/8dceb763-0681-45e3-a0fe-d3930dc00c60" />


Edit the replicaset
```
kubectl edit rs/nginx-54c98b4f84
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/9e5d9bce-457a-4881-8851-50b5a611181c" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2b896a9f-d966-46f8-a2c4-cd844ba1eacf" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/45c4fd43-f95f-4ec9-8e1d-3972f2eb44cc" />


Edit the pod
```
kubectl edit pod/nginx-54c98b4f84-kgztm
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0f35d798-f7d4-479b-866e-386cd94494dc" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/d697583a-d521-44cc-a8a4-9dd15373a6dd" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7282402d-e835-4d80-84a9-a108a00ca24b" />

## Lab - Creating a Pod and getting inside the Pod shell
```
kubectl debug -it nginx-54c98b4f84-kgztm --image=nginx:latest --target=nginx -- /bin/sh
ls
cat /etc/resolv.conf
exit
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4d256648-1473-4221-af90-e33048e7575b" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/81e8a893-0a23-4e60-9d3d-ec13cccecd81" />

The /etc/resolv.conf points a DNS Service running within the Kubernetes cluster, this DNS Pod supports the service discovery within Kubernetes cluster. 
Service Discovery is nothing but resolving a service name to its respective IP Address.


## Info - Kubernetes Service
<pre>
- Service represents a group of load-balanced pods from a single deployment
- this is a way we can expose our application within the cluster or to the external world
- when we create service, its get a unique name and service IP
- the name of the service can be used in your application 
</pre>

## Lab - Creating a clusterip internal service for our nginx deployment using imperative command
```
kubectl get deployments
kubectl expose deploy/nginx --type=ClusterIP --port=80

kubectl get services
kubectl get service
kubectl get svc

kubectl describe svc/nginx
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/8cd0e786-2729-40c9-9690-ac99a2ca4511" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7c57d71e-f8e6-495c-9b36-afe8361a0e1b" />

Testing/Accessing the clusterip internal service
```
kubectl debug -it nginx-54c98b4f84-5tgq7 --image=tektutor/hello-ms:1.0 --target=nginx -- /bin/sh
wget http://nginx:80
cat index.html
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/24473099-507d-4eb1-990b-0226e7f36f73" />


### Lab - Getting all the events related to Pod with a particular label
```
kubectl get pods -l app=nginx -n jegan -o name | \                         
pipe> xargs -I{} kubectl describe {} -n jegan | grep -A20 "Events"
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/bf7eb859-3005-4344-8152-ef5004f29184" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4c825221-ec3e-41d7-adb7-4c9c3a651eac" />

## Info - ReplicationController
<pre>
- is one of the resource supported by Kubernetes
- this was the way we could deploy stateless applications into Kubernetes in older versions
- ReplicationController does 2 things
  - it supports rolling update
  - it also supports scale up/down
  - this kind of violates Single Responsibility Principle(SOLID)
  - this doesn't support declaratively performing rolling update or scale up/down, hence Google(K8s team) deprecated use of ReplicationController
  - in exchange of ReplicationController, they introduced Deployment and Replicaset for deploying stateless applications into Kubernetes
  - for backward compatility and to support legacy applications, ReplicationController is still there but we are not supposed use it for deploying new applications
  - Deployment Controller
    - supports Rolling update
  - ReplicaSet Controller
    - supports scale up/down
</pre>
