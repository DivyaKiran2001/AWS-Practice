**Load Balancer :**

A Load Balancer in AWS is a service that distributes incoming traffic (HTTP requests, API calls, TCP packets, etc.) across multiple backend servers such as:

ECS Tasks (containers)

EC2 instances

Lambda functions

IP addresses

**Types of Load Balancers :**

**1. Application Load Balancer (ALB) :**

**Layer:** 7 (HTTP/HTTPS) 

**Best for:** Web apps, APIs, microservices

**What it does:**

-> Routes traffic based on URL paths, hostnames, headers, query params, etc.

**Supports modern protocols like:**
HTTP/2

gRPC

WebSockets

Works perfectly with ECS (Fargate/EC2) and EKS.

**2. Network Load Balancer :**

A Network Load Balancer is a high-performance Layer 4 load balancer in AWS that distributes TCP/UDP/TLS traffic across backend servers with very low latency and supports static IP addresses.

**3.Gateway Load Balancer (GLB) :**

A Gateway Load Balancer is a special AWS load balancer designed to distribute network traffic to security appliances, such as:

Firewalls



