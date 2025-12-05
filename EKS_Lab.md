**Deploy Applications on Amazon Elastic Kubernetes Service (EKS)**
SPL-TF-100-COKUBE-1 - Version 1.0.1

© 2025 Amazon Web Services, Inc. or its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited. All trademarks are the property of their owners.

Note: Do not include any personal, identifying, or confidential information into the lab environment. Information entered may be visible to others.

Corrections, feedback, or other questions? Contact us at AWS Training and Certification.

**Lab overview**
In this lab, you take on the role of a cloud engineer at AnyCompany, a rapidly growing startup aiming to modernize its application deployment strategy. Your primary objective is to implement containerization and orchestration technologies using Amazon Elastic Kubernetes Service (EKS) to enhance the company’s cloud infrastructure.

You begin by creating an Amazon EKS cluster, which serves as the foundation for AnyCompany’s new containerized application environment. This process involves configuring the control plane, worker nodes, and VPC networking essential for Kubernetes operations. You use the eksctl command line utility to set up the cluster, gaining hands-on experience with the underlying infrastructure components managed by AWS.

Next, you deploy a sample containerized application to your newly created EKS cluster. This task demonstrates the power of Kubernetes in managing containerized workloads and introduces you to key concepts such as pods, deployments, and services. Using kubectl, the Kubernetes command-line tool, you interact with the cluster to deploy the application, simulating a real-world scenario of launching a new service at AnyCompany.

Finally, you verify the successful deployment by accessing the application through a web interface. This step showcases the end-to-end process of containerizing an application, deploying it to Kubernetes, and exposing it to the internet, mirroring the actual workflow you’d follow when launching new services for AnyCompany.

By completing this lab, you gain practical experience in creating and managing Kubernetes clusters on AWS, deploying containerized applications, and understanding the integration between AWS services and Kubernetes components. These skills are crucial for AnyCompany’s transition to a more flexible, scalable, and efficient application deployment strategy, positioning the company for future growth and technological advancement.

Objectives
By the end of this lab, you should be able to do the following:

**Create an Amazon EKS Cluster.**
Deploy a containerized application in an Amazon EKS cluster.
Verify that the application is functioning correctly.
Prerequisites
This lab requires:

Access to a computer with Microsoft Windows, Mac OS X, or Linux (Ubuntu, SuSE, or Red Hat).
A modern internet browser such as Chrome or Firefox.
Technical knowledge prerequisites
To successfully complete this lab, you should have:

Basic understanding of containerization concepts and Docker.
Familiarity with Kubernetes fundamentals.
Basic knowledge of AWS services, particularly EC2 and IAM.
Comfort with using command-line interfaces (CLI), especially in a Linux environment.
Basic understanding of networking concepts, including VPCs and load balancing.
Icon key
Various icons are used throughout this lab to call attention to different types of instructions and notes:

 Command: A command that you must run
 Caution: Information of special interest or importance (not so important to cause problems with the equipment or data if you miss it, but it could result in the need to repeat certain steps).
 Copy edit: A time when copying a command, script, or other text to a text editor (to edit specific variables within it) might be easier than editing directly in the command line or terminal.
 Expected output: Sample output to verify command results
 Note: A hint, tip, or important guidance
 Learn more: Where to find more information.
 Refresh: When to refresh your browser
 Task complete: Conclusion or summary point
Start lab
To launch the lab, at the top of the page, choose Start Lab.

 Caution: You must wait for the provisioned AWS services to be ready before you can continue.

To open the lab, choose Open Console .

You are automatically signed in to the AWS Management Console in a new web browser tab.

 Warning: Do not change the Region unless instructed.

Common sign-in errors
Error: Choosing Start Lab has no effect
In some cases, certain pop-up or script blocker web browser extensions might prevent the Start Lab button from working as intended. If you experience an issue starting the lab:

Add the lab domain name to your pop-up or script blocker’s allow list or turn it off.
Refresh the page and try again.
**Task 1: Set up the lab environment**
In this task, you access the VS Code IDE environment and verify that all necessary tools are installed and configured correctly for working with Amazon EKS.

Task 1.1: Access the VS Code IDE
Copy the LabWorkspaceURL value from the panel to the left of these instructions, paste it into a new browser tab, and navigate to the page.

Copy the LabWorkspacePassword value from the panel to the left of these instructions, paste it in the *Password* field and login.

A Visual Studio Code IDE displays.

 Note: Prior experience with VS Code IDE is not required.

Select the  Notifications icon in the very bottom right corner of the VS Code IDE (the bell icon) to clear any notifications that appeared when you opened the VS Code IDE.

The lower pane includes the following five tabs:

PROBLEMS
OUTPUT
DEBUG CONSOLE
TERMINAL
PORTS
Use the bash terminal for this lab.

 Note: When you attempt to paste into the VS Code IDE for the first time, it will prompt you about this action. Choose Allow.

Task 1.2: Verify pre-installed tools
In this task, you verify the required tools for Kubernetes cluster management and learn their roles in cluster operations. The two essential Kubernetes management tools: kubectl and eksctl serve different but complementary purposes:

kubectl

Manages applications and resources within a cluster.
Handles deployments, pods, and services.
Monitors cluster health and performance.
Executes commands against Kubernetes clusters.
eksctl

Specializes in Amazon EKS cluster operations.
Manages cluster infrastructure and configuration.
Handles node group scaling and updates.
Streamlines IAM integration for EKS.
Together, these tools provide a complete command-line interface for managing both the Kubernetes infrastructure and the applications running on it.

 Command: To verify the AWS CLI installation, run the following command:


aws --version
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************
aws-cli/2.23.6 Python/3.12.6 Linux/6.1.119-129.201.amzn2023.x86_64 exe/x86_64.amzn.2023
 Learn more: The AWS CLI version 2.x or higher supports all EKS features. Version 1.x has limited EKS functionality.

 Command: To verify kubectl installation and understand its version compatibility, run the following command:


kubectl version --output=yaml --client
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************
clientVersion:
  buildDate: "2024-12-14T09:56:26Z"
  compiler: gc
  gitCommit: e736f5fca01e6ac7c8d9f45cde3fddf18ea509d5
  gitTreeState: clean
  gitVersion: v1.32.0-eks-5ca49cb
  goVersion: go1.23.3
  major: "1"
  minor: 32+
  platform: linux/amd64
kustomizeVersion: v5.5.0
 Note: The kubectl version must be within one minor version of your cluster’s Kubernetes version. For example, with a cluster running Kubernetes 1.32, kubectl version can be 1.31, 1.32, or 1.33.

 Command: To verify the eksctl installation, run the following command:


eksctl version
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************
0.202.0
Task 1.3: Verify AWS credentials and set environment variables
In this task, you verify the pre-configured AWS credentials and set up the necessary environment variables for working with Amazon EKS.

 Command: To verify the AWS credentials, run the following command:


aws sts get-caller-identity
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************
{
    "UserId": "AROA3GYERRES7UFILODM2:i-0232a90564e2ab7d0",
    "Account": "111111111111",
    "Arn": "arn:aws:sts::111111111111:assumed-role/LabStack-awsstudent-hkG8f8mVgJ1BKd-VSCodeInstanceRole-q99sWCVQtBwm/i-0232a90564e2ab7d0"
}
 Command: To save your working region and account ID to shell variables, run the following command:


TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
export AWS_REGION=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
ACCOUNT_ID=$(aws sts get-caller-identity --query 'Account' --output text)
echo "Your AWS Account ID is $ACCOUNT_ID and you are working in the $AWS_REGION region"
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                Dload  Upload   Total   Spent    Left  Speed
100    56  100    56    0     0  10250      0 --:--:-- --:--:-- --:--:-- 11200
Your AWS Account ID is 111111111111 and you are working in the us-west-2 region
 Task complete: You successfully configured your lab environment, verified the required tools, and set up AWS credentials for EKS operations.

**Task 2: Create an Amazon EKS cluster**
In this task, you create and configure an Amazon EKS cluster. An EKS cluster consists of two main components:

A control plane that manages the overall state of the cluster.
Worker nodes that run your applications.
Task 2.1: Create and configure Amazon EKS cluster access
In this task, you use eksctl to create an EKS cluster with managed node groups. Managed node groups are a feature of Amazon EKS that automate the provisioning and lifecycle management of Amazon EC2 instances (nodes) in your Kubernetes cluster. A key advantage of managed node groups is their integration with Amazon EC2 Auto Scaling groups, which automatically adjust the number of nodes based on your application’s demands. This means your cluster can scale out to handle increased workloads and scale in during periods of lower activity, optimizing both performance and cost. Without managed node groups, you would need to manually provision EC2 instances, configure them as Kubernetes nodes, and manage their lifecycle and scaling, which can be complex and time-consuming. By using managed node groups, you simplify cluster operations, ensure your applications have the right amount of computing resources, and benefit from automatic updates and patching of nodes.

 Command: To create an Amazon EKS cluster with a managed node group, run the following command:


eksctl create cluster \
--name eks-lab-cluster \
--nodegroup-name worknodes-1 \
--node-type t3.medium \
--nodes 2 \
--nodes-min 1 \
--nodes-max 3 \
--managed \
--version 1.32 \
--region ${AWS_REGION}
 Learn more: When creating an EKS cluster with eksctl, the utility creates:

A new Virtual Private Cloud (VPC).
Three public and three private subnets across different Availability Zones.
Required security groups and IAM roles.
Amazon EKS control plane and worker nodes.
Understanding the command flags:

Cluster Configuration

eksctl create cluster: Initiates the cluster creation process.
–name eks-lab-cluster: Assigns a unique identifier to your cluster.
–version 1.32: Sets the Kubernetes version for your cluster.
–region ${AWS_REGION}: Determines where AWS creates your cluster resources.
Node Group Settings

–nodegroup-name worknodes-1: Names the group of worker nodes.
–node-type t3.medium: Selects the EC2 instance type for worker nodes.
–managed: Enables AWS to handle node group maintenance and updates.
Auto Scaling Configuration

–nodes 2: Sets the initial number of worker nodes.
–nodes-min 1: Defines the minimum nodes during scaling events.
–nodes-max 3: Sets the maximum nodes allowed during high demand.
 Note: While eksctl creates a new VPC by default, you can also configure it to use existing network resources. Refer to Amazon EKS networking in the Additional resources section for more information.

 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************
2025-01-29 18:42:48 [ℹ]  eksctl version 0.202.0
2025-01-29 18:42:48 [ℹ]  using region us-west-2
2025-01-29 18:42:49 [ℹ]  setting availability zones to [us-west-2d us-west-2b us-west-2a]
2025-01-29 18:42:49 [ℹ]  subnets for us-west-2d - public:192.168.0.0/19 private:192.168.96.0/19
2025-01-29 18:42:49 [ℹ]  subnets for us-west-2b - public:192.168.32.0/19 private:192.168.128.0/19
2025-01-29 18:42:49 [ℹ]  subnets for us-west-2a - public:192.168.64.0/19 private:192.168.160.0/19
2025-01-29 18:42:49 [ℹ]  nodegroup "worknodes-1" will use "" [AmazonLinux2/1.32]
2025-01-29 18:42:49 [ℹ]  using Kubernetes version 1.32
2025-01-29 18:42:49 [ℹ]  creating EKS cluster "eks-lab-cluster" in "us-west-2" region with managed nodes
2025-01-29 18:42:49 [ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial managed nodegroup
2025-01-29 18:42:49 [ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-west-2 --cluster=eks-lab-cluster'
2025-01-29 18:42:49 [ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "eks-lab-cluster" in "us-west-2"
2025-01-29 18:42:49 [ℹ]  CloudWatch logging will not be enabled for cluster "eks-lab-cluster" in "us-west-2"
2025-01-29 18:42:49 [ℹ]  you can enable it with 'eksctl utils update-cluster-logging --enable-types={SPECIFY-YOUR-LOG-TYPES-HERE (e.g. all)} --region=us-west-2 --cluster=eks-lab-cluster'
2025-01-29 18:42:49 [ℹ]  default addons vpc-cni, kube-proxy, coredns, metrics-server were not specified, will install them as EKS addons
2025-01-29 18:42:49 [ℹ]  
2 sequential tasks: { create cluster control plane "eks-lab-cluster", 
    2 sequential sub-tasks: { 
        2 sequential sub-tasks: { 
            1 task: { create addons },
            wait for control plane to become ready,
        },
        create managed nodegroup "worknodes-1",
    } 
}
2025-01-29 18:42:49 [ℹ]  building cluster stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:42:49 [ℹ]  deploying stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:43:19 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:43:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:44:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:45:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:46:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:47:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:48:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:49:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:50:49 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster"
2025-01-29 18:51:50 [ℹ]  waiting for CloudFormation stack "eksctl-eks-lab-cluster-cluster" 
^C
 Caution: Creating an EKS cluster requires approximately 10-15 minutes to set up all infrastructure components. To optimize your lab time, a pre-configured cluster exists for your use. Therefore, you can cancel the cluster creation process and connect to the pre-built cluster instead.

To stop creating the new cluster, press Ctrl+C.

 Expected output:

None, unless there is an error.

 Learn more: When an EKS cluster creation completes, eksctl saves a configuration file to ~/.kube/config. This file, known as the kubeconfig, contains cluster authentication credentials and connection details. For this lab, you use a pre-configured kubeconfig file saved in your environment.

 Command: To view the contents of the kubeconfig file and examine the pre-configured cluster access settings, run the following command:


cat /home/ec2-user/environment/scripts/config
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJVGtjc2JPcmZiV2N3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1qUXlNRFF3TVRkYUZ3MHpOVEF4TWpJeU1EUTFNVGRhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUUR6WnFJeG5keGdra1M0Y21kUVZtekcyd1pBUXhOeFFnYzBxamZ6SW54QnhTZ1I5WGRndGM3N1gzZkwKK05BU2lVRlFpMjV0U3F4bVFPU0t2YVdRb1htbm5UbFAxMEF5ZS9wc3ViQ0d1SjBBMVdTV2ZBcnc1VEYxbHRyTwpNRGJjWXJXNDBkZ2VabzNxTXZMREFGZ21vUFFpV1lHenIzN3RtakVTd2lubFUwK2tON2U5RGJJOUphNXFxTFdRCjJURThCdGs0My9JbUV2alhiSWw3QXBRc2FDYTRxQ2pQZGlYdmVZR1diOFFvaFNhcjBDU01YWUtialh4UXBtb1EKMU5pZVFiNmFYVGdiOHkrVzh3SjQzbUNMWEloWWxnc2YvaUhIYjJIdlIvUjBYcVVQR1RKM0ZNTy92RERqWVBHeQp1TVNTUllMZEFCT1k0NDVEa20wODk2c2E4MytaQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTRGphOExlbmo0N1dtS2tVQzRGMnpyd1dBYzRUQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ1ZXL0xQZ3F5TQp2c1UxMVFtN2RFUmNxOFJRckVxc3JWdy9samhpSFUyNTdudCt2VFVUMTRVeDlvS1hkZE9VUzFIcmxBc1dnUXpkCkpBcCtLWklBWkdsSXo3V2g4ekZnTWtnM1kzUTA3bkdOUDhKRnFTL2ZUOTRDeU9UWGJPVkpXNlpQcFg2aTRCaGQKeHZXTHM2NENQRldnR0YxM21XQWNlL1ZBNTE3bkZhUXRZNHdkbThMZ3ducWdhTlBwWThySjJoWll4Szk1WUh0Tgo4NUpDbEpDK3diakpvaVQvZzlkMldvTEZlWEQzSk9tcnVUUzJ5aFBlMTFzMUVJZEswZDF1Si92SUtTWGhERlV0CmRCVFVwdE4wRDlydEV5aTFPQXJGc3Z3aHFBcGJIWmFyOTRFWmJvVkpGdjcxSGdKcDVVSVFWMVc4cWlHcjJDZUwKMmd1WmFwQTd2MVJMCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://CF5CE07D346F4EED1754A3128FFEB124.gr7.us-west-2.eks.amazonaws.com
name: dev-cluster.us-west-2.eksctl.io
contexts:
- context:
    cluster: dev-cluster.us-west-2.eksctl.io
    user: i-0232a90564e2ab7d0@dev-cluster.us-west-2.eksctl.io
name: i-0232a90564e2ab7d0@dev-cluster.us-west-2.eksctl.io
current-context: i-0232a90564e2ab7d0@dev-cluster.us-west-2.eksctl.io
kind: Config
preferences: {}
users:
- name: i-0232a90564e2ab7d0@dev-cluster.us-west-2.eksctl.io
user:
    exec:
    apiVersion: client.authentication.k8s.io/v1beta1
    args:
    - eks
    - get-token
    - --output
    - json
    - --cluster-name
    - dev-cluster
    - --region
    - us-west-2
    command: aws
    env:
    - name: AWS_STS_REGIONAL_ENDPOINTS
        value: regional
    provideClusterInfo: false
 Learn more: The kubeconfig file organizes cluster access information into three main sections:

Cluster Information

API version for Kubernetes interactions.
Cluster endpoint and certificate data.
Cluster name and region details.
Authentication Settings

User credentials and contexts.
AWS IAM integration details.
Token generation commands.
Context Configuration

Default working context.
Cluster-to-user mappings.
Regional endpoint settings.
The key fields in the file are:

apiVersion: This field indicates the version of the Kubernetes API in which the kubeconfig file is written.
clusters: This section contains information about the Kubernetes clusters that the kubeconfig file can be used to access. In this case, there is only one cluster, dev-cluster.us-west-2.eksctl.io.
certificate-authority-data: This field contains the certificate authority data for the cluster, which is used to verify the server’s certificate.
current-context: This field indicates the current context, which is the context against which commands will be run by default.
users: This section contains information about the users that can be used to authenticate to the clusters. In this case, there is only one user, i-0232a90564e2ab7d0@dev-cluster.us-west-2.eksctl.io.
exec: This field contains a command to be executed to fetch user credentials. In this case, the command is aws eks get-token, which fetches a token for the specified cluster and region.
 Command: To configure access to the pre-built cluster, move the kubeconfig file into your home directory by running the following command:


mv /home/ec2-user/environment/scripts/config ~/.kube/config
 Expected output:

None, unless there is an error.

Now that you moved the kubeconfig file into your home directory, you should be able to connect to the cluster.

Task 2.2: Verify and configure the cluster
In this task, you verify the cluster’s status and configure its resources. You use various kubectl and eksctl commands to interact with your cluster.

 Command: To verify the cluster status, run the following command:


eksctl get cluster --region $AWS_REGION
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************
NAME            REGION         EKSCTL CREATED
dev-cluster     us-west-2       True
 Learn more: This command lists all EKS clusters in your region. The EKSCTL CREATED field indicates whether eksctl created the cluster.

 Note: The output might show both eks-lab-cluster (from your creation attempt) and dev-cluster (the pre-built cluster). For all remaining steps in this lab, use only the dev-cluster cluster.

 Command: To examine the cluster’s node group configuration, run the following command:


eksctl get nodegroup --cluster=dev-cluster --region $AWS_REGION
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

CLUSTER         NODEGROUP       STATUS  CREATED                 MIN SIZE        MAX SIZE        DESIRED CAPACITY        INSTANCE TYPE   IMAGE ID        ASG NAME                        TYPE
dev-cluster     dev-nodes       ACTIVE  2023-09-05T14:03:02Z    2               4               3                       t3.medium       AL2_x86_64      eks-dev-nodes-f2c532d3-aafd-02fc-3d87-9aeff059d5e0       managed
 Learn more: The output shows:

Node group status and creation time.
Auto Scaling group configuration.
Instance type and Amazon Machine Image (AMI) used.
Whether the node group is managed by AWS.
 Note: The output shows nodegroup dev-nodes with a desired capacity of 3 nodes. To optimize cluster resources for this lab’s requirements, the next step scales the nodegroup down to 2 nodes.

 Command: To reduce the nodegroup capacity from 3 nodes to 2 nodes, run the following command:


eksctl scale nodegroup --cluster=dev-cluster --nodes=2 --name=dev-nodes --region $AWS_REGION
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

2023-09-07 20:29:40 [ℹ]  scaling nodegroup "dev-nodes" in cluster dev-cluster
2023-09-07 20:29:41 [ℹ]  initiated scaling of nodegroup
2023-09-07 20:29:41 [ℹ]  to see the status of the scaling run `eksctl get nodegroup --cluster dev-cluster --region us-west-2 --name dev-nodes`
After verifying the cluster management capabilities of eksctl, the next step verifies how kubectl communicates with cluster resources.

 Command: To verify cluster connectivity and view its core components, run the following command:


kubectl cluster-info
 Expected output: The output should be similar to the following:


************************
**** EXAMPLE OUTPUT ****
************************
Kubernetes control plane is running at https://748E381CD720F24CE6AA7B647A8B8A81.gr7.us-west-2.eks.amazonaws.com
CoreDNS is running at https://748E381CD720F24CE6AA7B647A8B8A81.gr7.us-west-2.eks.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
 Learn more: The output confirms:

The control plane is operational.
CoreDNS is running (handles internal DNS resolution inside the cluster).
Your kubectl configuration can successfully communicate with the cluster.
 Consider: How did kubectl connect to the cluster? Amazon EKS uses IAM to provide authentication to your Kubernetes cluster, but it still relies on native Kubernetes Role Based Access Control (RBAC) for authorization. This means that IAM is only used for authentication of valid IAM entities. All permissions for interacting with your Amazon EKS cluster’s Kubernetes API is managed through the native Kubernetes RBAC system.

Finally, verify that your worker nodes are properly configured and ready to accept workloads.

 Command: To view the status of your worker nodes, run the following command:


kubectl get nodes
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

NAME                                          STATUS   ROLES    AGE     VERSION
ip-10-0-39-141.us-west-2.compute.internal   Ready      <none>   4m18s   v1.32.0-eks-aeac579
ip-10-0-59-184.us-west-2.compute.internal   Ready      <none>   42m     v1.32.0-eks-aeac579
 Learn more: The output shows:

Two worker nodes in ‘Ready’ status
Each node’s internal IP address and hostname
The Kubernetes version running on each node
How long each node has been part of the cluster
 Task complete: You successfully attempted a cluster creation process, connected to a pre-built EKS cluster, adjusted its node group capacity, and verified cluster connectivity. The cluster is now ready for deploying applications.

**Task 3: Deploy a sample containerized application**
In this task, you deploy a multi-tier employee directory application into your Amazon EKS cluster. The application architecture consists of:

A containerized Python frontend for the user interface.
A containerized .NET backend API for business logic.
An Amazon DynamoDB table for data persistence.
The application uses Kubernetes native features for deployment and scaling:

Separate deployments for frontend and backend components.
ReplicaSets maintaining two pods for each component.
Kubernetes services for network connectivity.
External access through load balancer services.
The following diagram illustrates this architecture:

Kubernetes architecture

Image description: The preceding diagram shows the Kubernetes application components: two deployments (frontend and backend), each managed by ReplicaSets maintaining two pods. Services front each deployment to handle network routing.

The application leverages an Amazon DynamoDB table for employee data storage, maintaining data separation from the Kubernetes cluster. The next step creates the database table.

Task 3.1: Create the database table
In this task, you create an Amazon DynamoDB table that stores the employee directory data.

 Command: To create a DynamoDB table for the application, run the following command:


aws dynamodb create-table --table-name Employees --attribute-definitions AttributeName=id,AttributeType=S --key-schema AttributeName=id,KeyType=HASH --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

{
    "TableDescription": {
        "AttributeDefinitions": [
            {
                "AttributeName": "id",
                "AttributeType": "S"
            }
        ],
        "TableName": "Employees",
        "KeySchema": [
            {
                "AttributeName": "id",
                "KeyType": "HASH"
            }
        ],
        "TableStatus": "CREATING",
        "CreationDateTime": "2023-08-24T15:25:32.247000+00:00",
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "ReadCapacityUnits": 1,
            "WriteCapacityUnits": 1
        },
        "TableSizeBytes": 0,
        "ItemCount": 0,
        "TableArn": "arn:aws:dynamodb:us-west-2:111111111111:table/Employees",
        "TableId": "bb3ea3b6-b1fb-4756-83a1-f16ef6f0145b",
        "DeletionProtectionEnabled": false
    }
}
 Learn more: The DynamoDB table creation command configures four essential components:

Table Name

Command: aws dynamodb create-table --table-name Employees.
Purpose: Creates a new table called Employees.
Attribute Definition

Command: –attribute-definitions AttributeName=id,AttributeType=S.
Purpose: Defines id as a String attribute for unique record identification.
Key Schema

Command: –key-schema AttributeName=id,KeyType=HASH.
Purpose: Sets id as the primary key using HASH type for optimal data access.
Throughput Capacity

Command: –provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1.
Purpose: Determines the maximum number of read and write operations the table can handle per second, with one unit allocated for each operation type.
Task 3.2: Deploy the application frontend
In this task, you deploy the frontend component and examine the Kubernetes resources it creates. First, look at the current state of your cluster.

 To view existing deployments, services, and pods in your cluster, run the following command:


kubectl get deployment,service,pod --all-namespaces
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

NAMESPACE     NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns          2/2     2            2           60m
kube-system   deployment.apps/metrics-server   2/2     2            2           60m

NAMESPACE     NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes                  ClusterIP   172.20.0.1     <none>        443/TCP                  64m
kube-system   service/eks-extension-metrics-api   ClusterIP   172.20.41.70   <none>        443/TCP                  64m
kube-system   service/kube-dns                    ClusterIP   172.20.0.10    <none>        53/UDP,53/TCP,9153/TCP   60m
kube-system   service/metrics-server              ClusterIP   172.20.1.181   <none>        443/TCP                  60m

NAMESPACE     NAME                                  READY   STATUS    RESTARTS   AGE
kube-system   pod/aws-node-f64jg                    2/2     Running   0          5m31s
kube-system   pod/aws-node-ktxjd                    2/2     Running   0          56m
kube-system   pod/coredns-59997bbc96-kqfjl          1/1     Running   0          45m
kube-system   pod/coredns-59997bbc96-xgpqj          1/1     Running   0          25m
kube-system   pod/kube-proxy-fkxtn                  1/1     Running   0          5m31s
kube-system   pod/kube-proxy-sq9fg                  1/1     Running   0          56m
kube-system   pod/metrics-server-6d8cb85f76-kwgtz   1/1     Running   0          52m
kube-system   pod/metrics-server-6d8cb85f76-shtd6   1/1     Running   0          25m
 Learn more: The kubectl get command retrieves information about Kubernetes resources:

Resource Types

Deployments: Application blueprints that define desired state.
Pods: Individual units of compute running containers.
Services: Network endpoints that route traffic to pods.
Namespaces: Logical boundaries for organizing resources.
Command Structure

Format: kubectl get [TYPE] [NAME] [NAMESPACE].
Multiple resource types can be requested simultaneously.
–all-namespaces shows resources across all logical boundaries.
 Note: In the output, you see the cluster’s core components:

A DNS proxy deployment
System services for pod communication
Supporting pods in the kube-system namespace
Before deploying your application, understand how Kubernetes uses manifests for resource definition:

 Learn more: Kubernetes follows a declarative model:

Manifests define the desired state in YAML or JSON format.
You specify what you want, not how to achieve it.
Kubernetes continuously works to maintain this desired state.
Resources are created from these manifest definitions.
Now, prepare to deploy the frontend application. The source code is pre-loaded in an S3 bucket for your use.

 Copy edit: Copy the following command and paste it into your favorite text editor to edit the command.


FRONTEND_S3=FRONT_END_SOURCE_CODE_URL
 Copy edit: Replace the FRONT_END_SOURCE_CODE_URL placeholder value with the FrontendSourceCodeURL value that is listed to the left of these instructions.

 Command: To create a shell variable to store the S3 URL to the front end code location, run the edited command.

 Expected output:

None, unless there is an error.

Next, create a manifest that defines how Kubernetes should deploy your frontend application. The manifest specifies a container that retrieves application code from Amazon S3, sets up the Python environment, and runs the application.

 Command: To create the frontend deployment manifest, run the following command:


cat <<EOF > ~/environment/deployment-frontend.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: frontend
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - image: python:3.10-slim
        name: frontend
        env:
          - name: API_ENDPOINT
            value: "http://backend.default.svc.cluster.local"
          - name: FLASK_APP
            value: "application.py"
        command: ["/bin/sh","-c"]
        args: 
         - mkdir /app ; 
           cd /app ;  
           apt update ; 
           apt install wget -y curl unzip ;
           curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" ;
           unzip awscliv2.zip ;
           ./aws/install ;
           aws s3 cp $FRONTEND_S3 . ;
           unzip DirectoryFrontend.zip ;
           cd DirectoryFrontend/ ;
           pip3 install -r requirements.txt ;
           flask run --host 0.0.0.0 --port 80
EOF
 Expected output:

None, unless there is an error.

 Learn more: The deployment manifest contains these key fields:

Resource Definition

apiVersion: apps/v1 and kind: Deployment: Specifies the Kubernetes API version and resource type.
metadata: Defines the deployment name and labels for identification.
spec: Contains the complete deployment configuration.
Deployment Configuration

replicas: 2: Maintains two identical pods for the application.
selector: Links the deployment to its managed pods.
template: Defines the pod configuration pattern.
Container Settings

containers: Lists the containers to run in each pod.
image: python:3.10-slim: Specifies the base Python container image.
name: frontend: Assigns a name to the container.
env: Configures environment variables for:
Backend API endpoint.
Flask application settings.
command and args: Define the container startup sequence:
Application setup.
Dependency installation.
Code retrieval from S3.
Application launch.
 Note: Each field in the manifest follows Kubernetes’ declarative configuration model, where you specify the desired state for your application.

With the frontend manifest defined, you can now apply it to create the deployment in your cluster.

 Command: To create the frontend deployment, run the following command:


kubectl apply -f ~/environment/deployment-frontend.yaml
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

deployment.apps/frontend created
 Note: The kubectl apply command implements the resource definitions in your cluster, creating or updating them as necessary.

While the frontend pods are now running, they aren’t yet accessible from outside the cluster. To enable external access, you need to create a Kubernetes service.

 To define the frontend service, create a manifest with the following content:


cat <<EOF > ~/environment/service-frontend.yaml
apiVersion: v1
kind: Service
metadata:
    labels:
      app: frontend
    name: frontend
spec:
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
    selector:
      app: frontend
    type: LoadBalancer
EOF
 Learn more: This manifest creates a service called frontend that acts as a network interface for your application:

Service Identification

apiVersion: v1 and kind: Service: Defines a Kubernetes Service resource.
metadata.name: frontend: Names the service ‘frontend’.
metadata.labels: Tags the service for identification and selection.
Traffic Routing

spec.selector: Directs traffic to pods labeled ‘app: frontend’.
spec.ports: Configures the service’s networking.
port: 80: Service listens on port 80.
targetPort: 80: Forwards to port 80 on the pods.
External Accessibility

spec.type: LoadBalancer: Provisions an AWS load balancer for internet access.
This configuration establishes a network endpoint that routes incoming HTTP traffic to your frontend pods, making your application accessible from outside of the cluster.

 Command: To create the frontend service in your cluster, run the following command:


kubectl apply -f ~/environment/service-frontend.yaml
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

service/frontend created
 Command: To verify the deployment of both the frontend pods and service, run the following command:


kubectl get deployment,service,pod
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/frontend   2/2     2            2           62m

NAME                 TYPE           CLUSTER-IP     EXTERNAL-IP                                                               PORT(S)        AGE
service/frontend     LoadBalancer   172.20.5.236   aa342c97ec5f6499280c38cec2c5a4b0-111111111111.us-west-2.elb.amazonaws.com   80:32742/TCP   19m
service/kubernetes   ClusterIP      172.20.0.1     <none>                                                                    443/TCP        152m

NAME                           READY   STATUS    RESTARTS   AGE
pod/frontend-86454f8fb-5nd8r   1/1     Running   0          62m
pod/frontend-86454f8fb-nplm5   1/1     Running   0          62m
 Note: This output confirms:

Two frontend pods are running
The frontend service is exposed via a LoadBalancer
The LoadBalancer has been assigned an external IP (DNS name)
If the status is not Running, verify that you entered the previous commands correctly and repeat them if necessary.

Copy the External IP and paste it into a new web browser tab. Be sure to prepend the LoadBalancer URL with http://.

 Caution: If the webpage fails to load, the backend load balancer may not yet be fully online. Wait a couple of minutes and then refresh the page.

Once the application loads, you should see the employee directory homepage. Initially, it displays an empty directory.

The LoadBalancer URL should resemble this format:

aa342c97ec5f6499280c38cec2c5a4b0-111111111111.us-west-2.elb.amazonaws.com

 Caution: If the page doesn’t load immediately, the load balancer might still be initializing. Wait a few minutes and refresh. If issues persist, verify that the frontend deployment’s status is Running.

 Note: Upon loading, you’ll see an API error message in the application frontend. This error is expected at this stage, as you haven’t yet deployed the backend application or established the database connection.

Frontend API connection error

Image description: The application frontend displays an error message indicating an API exception has occurred.

Task 3.3: Deploy the application backend
In this task, you deploy the backend component of your application to complement the frontend you’ve just set up.

Return to your VS Code IDE terminal.

 Note: Just like the source code for the application frontend, the code for the backend has also been saved to an S3 bucket.

 Copy edit: Copy the following command and paste it into your favorite text editor to edit the command.


BACKEND_S3=BACK_END_SOURCE_CODE_URL
 Copy edit: Replace the BACK_END_SOURCE_CODE_URL placeholder value with the BackendSourceCodeURL value that is listed to the left of these instructions.

 Command: To create a shell variable to store the S3 URL to the back end code location, run the edited command.

 Expected output:

None, unless there is an error.

Next, create a manifest that defines how Kubernetes should deploy your backend application. The manifest specifies a container that retrieves the backend code from Amazon S3, sets up the .NET environment, and runs the application.

 Command: To create the backend deployment manifest, run the following command:


cat <<EOF > ~/environment/deployment-backend.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: backend
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - image: mcr.microsoft.com/dotnet/sdk:6.0
        env:
        - name: ASPNETCORE_URLS
          value: "http://+:80"
        command: ["/bin/sh","-c"]
        args: 
         - mkdir /app ; 
           cd /app ;
           apt update ;
           apt install wget -y curl unzip ;
           curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" ;
           unzip awscliv2.zip ;
           ./aws/install ;
           aws s3 cp $BACKEND_S3 . ;
           unzip DirectoryBackend.zip ;
           cd DirectoryBackend/ ;
           dotnet run
        name: backend
        resources: {}
EOF
 Learn more: The backend deployment manifest contains these key fields:

Resource Definition

apiVersion: apps/v1 and kind: Deployment: Specifies the Kubernetes API version and resource type.
metadata: Defines the deployment name and labels for identification.
spec: Contains the complete deployment configuration.
Deployment Configuration

replicas: 2: Maintains two identical pods for the application.
selector: Links the deployment to its managed pods.
template: Defines the pod configuration pattern.
Container Settings

containers: Lists the containers to run in each pod.
image: mcr.microsoft.com/dotnet/sdk:6.0: Specifies the base .NET SDK container image.
name: backend: Assigns a name to the container.
env: Configures environment variables for ASP.NET Core.
command and args: Define the container startup sequence:
Application setup.
Dependency installation.
Code retrieval from S3.
Application launch.
 Note: This manifest creates a deployment with two replicas, each running a .NET application in a container.

While the backend doesn’t have a user-facing interface, it requires a service to facilitate communication with the frontend and the Amazon DynamoDB database.

 Command: To create the backend service manifest, run the following command:


cat <<EOF > service-backend.yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  selector:
    app: backend
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
EOF
 Expected output:

None, unless there is an error.

 Learn more: This manifest creates a service called backend that facilitates internal communication:

Service Identification

apiVersion: v1 and kind: Service: Defines a Kubernetes Service resource.
metadata.name: backend: Names the service ‘backend’.
Traffic Routing

spec.selector: Directs traffic to pods labeled ‘app: backend’.
spec.ports: Configures the service’s networking.
port: 80: Service listens on port 80.
targetPort: 80: Forwards to port 80 on the pods.
Internal Accessibility

spec.type: ClusterIP: Makes the service only internally accessible within the cluster.
This configuration creates an internal endpoint for pod-to-pod communication, allowing the frontend to interact with the backend.

 Command: To deploy both the backend service and deployment, run the following command:


kubectl apply -f service-backend.yaml,deployment-backend.yaml
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

deployment.apps/backend created
service/backend created
Now that you’ve deployed both frontend and backend components, review all the resources in your cluster.

 Command: To display information about all deployed resources in your cluster, run the following command:


kubectl get all
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

NAME                           READY   STATUS    RESTARTS   AGE
pod/backend-65946ddc59-6kjhr   1/1     Running   0          3m31s
pod/backend-65946ddc59-x7jvp   1/1     Running   0          3m31s
pod/frontend-7f49fffc5-w89d9   1/1     Running   0          18m11s
pod/frontend-7f49fffc5-x6bfd   1/1     Running   0          18m11s

NAME                 TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)        AGE
service/backend      ClusterIP      172.20.38.104    <none>                                                                    80/TCP         3m31s
service/frontend     LoadBalancer   172.20.130.197   aa342c97ec5f6499280c38cec2c5a4b0-111111111111.us-west-2.elb.amazonaws.com   80:32007/TCP   17m44s
service/kubernetes   ClusterIP      172.20.0.1       <none>                                                                    443/TCP        28m22s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/backend    2/2     2            2           3m31s
deployment.apps/frontend   2/2     2            2           18m11s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/backend-65946ddc59   2         2         2       3m31s
replicaset.apps/frontend-7f49fffc5   2         2         2       18m11s
 Note: This output confirms:

Both frontend and backend have two pods running
The frontend service is exposed via a LoadBalancer
The backend service is internally accessible (ClusterIP)
All deployments and replicasets are properly configured
 Task complete: You successfully deployed both the frontend and backend components of your application to the EKS cluster.

**Task 4: Verify the deployed application**
In this task, you verify that your application is accessible and functioning correctly. You’ll retrieve the LoadBalancer URL, access the application through a web browser, and test its functionality.

Task 4.1: Retrieve the load balancer DNS name
In this task, you use kubectl to obtain the DNS name of the load balancer associated with your frontend service. This DNS name is crucial for accessing your application.

 Command: To find the frontend load balancer address, run the following command:


kubectl get service frontend --output wide
 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

NAME       TYPE           CLUSTER-IP     EXTERNAL-IP                                                               PORT(S)        AGE   SELECTOR
frontend   LoadBalancer   172.20.12.31   aa342c97ec5f6499280c38cec2c5a4b0-111111111111.us-west-2.elb.amazonaws.com   80:32625/TCP   24m   app=frontend
 Learn more: Including the –output wide flag makes kubectl provide additional information about the service. This can be useful when you require a more comprehensive understanding of the service’s configuration and status.

 Note: The EXTERNAL-IP field contains the DNS name of your LoadBalancer. You use this to access your application.

Task 4.2: Access the application website
In this task, you use the load balancer DNS name to access your application through a web browser. You also observe how Kubernetes distributes requests across multiple pods.

Copy the External IP and paste it into a new web browser tab. Be sure to prepend the LoadBalancer URL with http://.

 Caution: If the webpage fails to load, the backend load balancer may not yet be fully online. Wait a couple of minutes and then refresh the page.

Once the application loads, you should see the employee directory homepage. Initially, it displays an empty directory.

 Note: Remember that your deployment includes two frontend and two backend pods. This means there are two instances of each application component running in your cluster, providing redundancy and allowing for load distribution.

The application displays an empty employee directory.

Image description: In the preceding image, the application displays an empty employee directory. Beneath the directory table are two strings showing the frontend pod ID and the service ID.

 Note: Recall that the kubectl get all command showed that there were two frontend and two backend pods. In this case, it means that there are two instances of each container running in your cluster.

Refresh the page and note the ID displayed next to Frontend → Hello from frontend-xxxyyyzzz and Service → Hello from backend-aaabbbccc. These IDs correspond to the pod names in your cluster. Each time you refresh, these values are updated to show which frontend pod you are connected to and which backend pod it is communicating with. This demonstrates how requests are distributed across different pods in your cluster.

Task 4.3: Confirm the application is running correctly
In this task, you verify that all components of your application are functioning properly by adding a new employee record to the directory. This process tests the frontend, backend, and database interactions.

Return to your VS Code IDE terminal.

 Command: To enable the backend to interact with DynamoDB, run the following command:


kubectl set serviceaccount deployment backend dynamo-sa
 Learn more: Kubernetes service accounts provide identities for pod processes. By mapping a service account to an AWS IAM role, you grant pods access to AWS services. Refer to Kubernetes service accounts in the Additional resources section for more information.

 Expected output:


************************
**** EXAMPLE OUTPUT ****
************************

deployment.apps/backend serviceaccount updated
 Note: The kubectl set [RESOURCE/IMAGE/SERVICEACCOUNT] [NAME] command allows you to modify existing resources in your cluster, including service account assignments.

With the service account now linked to the backend deployment, you can verify the application’s ability to interact with the DynamoDB database by adding a new employee record.

Return to your application in the web browser and navigate to the Corporate Directory - Add/Edit page.

To create a user, enter the following details:

For Full Name, enter John Doe.
For Location, enter San Francisco.
For Job Title, enter Architect.
Choose the checkbox next to Linux User.
Scroll to the bottom of the page and choose Save to add the employee.

Verify that the application displays a confirmation message and that John Doe appears in the employee directory.

 Note: The successful addition of John Doe to the employee directory demonstrates that your EKS-based application is fully operational, with all components—frontend, backend, and database—working together seamlessly.

The application directory includes a new user.

Image description: In the preceding image, the application shows that John Doe has been saved to the employee directory.

 Task complete: You successfully deployed a fully functional application on Amazon EKS. The frontend serves web pages, the backend processes requests and interacts with DynamoDB, and the entire system demonstrates core Kubernetes concepts including pod replication and service discovery.
