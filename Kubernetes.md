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














