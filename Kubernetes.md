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




