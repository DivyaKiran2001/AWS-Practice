**Kubernetes :**

Kubernetes is nothing but a cluster . It consists of group of nodes. It contains master and worker nodes

Kubernetes supports the below features :
-> Auto scaling
-> Auto healing (Kubernetes controls and fix the damage)
-> Single Host of nature

**Pod :** Definition of how to run a container in YAML format.It is nothing but a wrapper
-> A pod can contain one or more containers

**Benefits of One or more containers in Pods :**

-> Shared Network
-> Shared Storage

**kubectl :** A command line interface (CLI) for kubernetes 
-> You can use local to run kubernetes using kind , k3s , minikube , microk8s

-> Using minikube you can create a cluster

**Commands :**

1. kubectl create -f pod.yaml :
   The command kubectl create -f pod.yaml tells Kubernetes to create the resources defined in the YAML file. Usually, it creates a Pod with the configuration provided.
3. kubectl get pods
4. kubectl get pods -o wide
5. kubectl delete pod <pod_name>
6. kubectl logs <pod_name> :
   To get the logs of the application
7. kubectl describe pod <pod_name> :
   To get in detail information about the pod

**Kubernetes architecture :**

The architecture consists of two main components.It contains Control plane (Master node) and data plane (Worker node)

Kubernetes follows a master–worker (control plane–node) architecture where the Control Plane makes decisions and Worker Nodes execute the workloads.


**Data Plane components :**

**Kubelet :**

The kubelet is an agent that runs on every worker node.
Its main job: Make sure the containers that Kubernetes wants → are actually running on the node.

**Kube-Proxy :**

kube-proxy is a networking component that runs on every worker node.
Its main job: Ensure network communication between pods, services, and external traffic.

**Container Runtime :**

A Container Runtime is the software responsible for running containers inside your pods.

**Control Plane components :**

**API Server:** the control center for all requests

**Scheduler:** where pods should run

**etcd:** the brain memory (stores cluster state).It is a key value store

**Controller Manager:** This component runs all the core Kubernetes controllers inside a single process.

-> **ReplicaSet Controller :** Ensures the desired number of pod replicas are always running.

-> **Node Controller :** Detects when a node fails and handles pod eviction or rescheduling.

-> **Deployment Controller :** Manages rollouts, rollbacks, and keeping Deployment state consistent.

-> **Endpoint Controller :** Updates Service → Pod mappings when pods join or leave.

**Cloud Controller Manager (CCM):**

The Cloud Controller Manager is responsible for integrating Kubernetes with cloud providers (AWS, GCP, Azure, etc.)

**Deployemnt :**

Deployment (yaml file) -> Replica Sets -> Creation of pod

**Service :**


A Service in Kubernetes is an abstract way to expose a group of Pods as a single, stable network endpoint.
It provides a permanent IP address, DNS name, and load balancing to ensure reliable access to Pods whose IPs constantly change.
Service works on the concept of labels and selectors

-> You have these advantages of service : Load Balancing , Service Discovery
-> A service can expose your application and can expose to outside world.
It contains three types :

**1. Cluster IP :**

ClusterIP is the default service type.
It makes your application accessible only inside the Kubernetes cluster.

👉 Meaning:

Gets an internal IP

Other pods inside the cluster can access it

Not reachable from your laptop or internet

**2. Nodeport Mode :**

NodePort exposes your application outside the cluster by opening a port on every node.

👉 Meaning:

Kubernetes assigns a port like 30080 on every node.
   
**3. Load Balancer :**

LoadBalancer exposes your application to the internet by creating a cloud load balancer (AWS ELB, GCP LB, Azure LB).
👉 Meaning:

You get a public IP

Users anywhere can access your application


**Service Discovery :**

The ability of applications (Pods) to automatically find and communicate with other applications (Services) inside the cluster without knowing their IP addresses.
**using labels and selectors**

-> Labels and selectors will be attached as the metadata in the yaml file so when we deploy the application using deployment.yaml file replica set will be created and create the pods with these labels.So when one pod goes down it will automatically create the pod with the same label.


**Practical :**

1. Build the image using docker build
2. create a deployment file (yaml file) and labels and selectors also update the container image and container port also
   kubectl apply -f deployment.yml
   kubetcl get pods
   kubectl get pods -o wide
   kubectl get pods -v=7
3. Now create a service yaml file of one of the types (Cluster IP,Nodeport,Load Balancer)
   kubectl apply -f service.yml
   kubectl get svc
   To edit the service
   kubectl edit svc


















