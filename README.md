# Learning Kubernetes

## About

This repo is intended to be my personal studying notes for k8s. It'll continue growing with time. I'll try my best to document all resources where I got the information from.

## Kubernetes
It is basically a cluster for running apps and an orchestrator of Cloud native micro-services apps

### K8S Components

#### 1- Masters , Nodes and DNS
##### Master:  
multi-master hight availability (control plane) [ 3 - 5 multi master recommended]

Services that make up the master (control plane)
  - API server
  - The cluster store (based on etcd (a distributed DB) )
  - The controller manager
  - The scheduler
  - The cloud controller manager (manage interaction with underlying cloud technologies. Instances, Load Balancing and storage)

##### Nodes:
The workers of k8s cluster

Duties:
  - Watch the API server for new work assignments
  - Execute new work assignments
  - Report back to the control plane

Consists of:
  - Kubelet (responsible for node registration process, watch for new work assignments and reporting to master)
  - Container runtime (CRI)
  - Network proxy (kube-proxy)

##### K8s DNS:
  - Every K8S cluster has an internal DNS service
  - DNS service has a static ip which is hard coded into all pods (All containers and pods know where to find it)
  - All new services automatically registered with the cluster's DNS so all components can find the service by name

### K8S Workflow

#### 1- Packaging apps
  - App package as a container
  - Wrapped in a pod
  - Deployed via a declarative manifest file (Deployment, DaemonSets, StatefulSets, CronJobs, etc..)

#### 2- Declarative configs and desired state
  - Declare the desired state of the application (micro-service) in a deployment file (manifest)
  - POST to k8s API server
  - k8s stores this in the cluster store as the application desired state
  - k8s implements the desired state on the cluster
  - k8s implements watch loops on the nodes to make sure current state of application doesn't vary from the desired state

### K8S Objects

#### 1- Pods

VMware -> vm
Docker Image -> Docker container
K8s ->  Pods

  - A single pod can only be scheduled to a single node
  - If a pod dies a brand new one is created with new IP and ID

#### 2-  Deployments
We normally deploy pods indirectly as part of something bigger like Deployments.

Deployments implements the controller and the Watch Loop to constantly observe the cluster making sure to meet the desired state.

#### 3- Services

Services provides reliable Networking for a set of pods. Provides a  reliable name, IP and load balancing requests to the pods behind it

Operates on TCP and UPD layer so no application intelligence routing

Services has label selectors that is a list of all the labels pods must posses in order for it to receive traffic from the service

## Useful commands

Running a particular image on a cluster

`kubectl run hello-minkube --image=gcr.io/google_containers/echoserver:1.4 --port=8080`

Expose this port to the host

NodePort: Allows us to have external ip address connected to the port that we specified with the kubectl run  

`kubectl expose deployment hello-minkube --type=NodePort`

`minikube service hello-minkube --url`

Before we can launch a container based on an image we have to create a pod definition

`kubectl create -f pod-goAndNode.yaml`

To delete a deployment

`kubectl delete deployment <deployment-name>`

To apply configs from file to cluster

`kubectl apply -f <file-path.yaml/json>`

Get information about nodes

`kubectl get nodes`

Get information about all deployments

`kubectl get deployment`

Get information about all running pods

`kubectl get pod`

Describe a pod

`kubectl describe pod <pod-name>`

Describe a node

`kubectl describe node <node-name>`

Export the port of a pod (create a new service)

`kubectl expose pod <podName> --port=<portNumber> --name=<name>`

Port forward that expose pod port to your local machine

`kuberctl port-forward <podName> <portNumber>`

Attach a pod

`kubectl attach <podName>`

Execute commands on a pod

`kuberctl exec <podName> -- <commands>`

Use shell on a pod

`kubectl exec -it <podName> bash`

Add label to pod after they are created

`kubectl label pods <podName> <key>=<value>`

Run a shell in a pod

`kubectl run -i --tty busybox --image=busybox`

Creating replicas for a deployment

`kubectl scale --replicas=<number> deployment/<deployment-name>`

Defining a LoadBalancer service

`kubectl expose deployment <deployment-name> --type=LoadBalancer --port=8080 --target-port=8080 --name=<name>`

To describe a services

`kubectl describe service tomcat-load-balancer`

To view status of deployment rollouts

`kubectl rollout status deployment <deployment-name>`

To view the history of rollout

`kubectl rollout history deployment/<deployment-name>`

To inspect a history of a revision
`kubectl rollout history deployment/<deployment-name> --revision=<revision-number>`

To set an image of a deployment

`kubectl set image deployment/<deployment-name> <container-name>=<image-name>`

To add labels to node

`kubectl label node <node-name> <key>=<value>`

node selectors (deployment.yaml file)

```
nodeSelector:
       storageType: ssd
```

Health check types
 1- readinessProbe
 2- livenessProbe
 3- commands

Name space convention

`<service-name>-<name-space>.svc.cluster.local`

Find DNS configuration

1- use busy busy box
2- locate `/etc/resolv.conf`

## Resources
1- The Kubernetes Book 2019
2- Kubernetes cheat sheet https://kubernetes.io/docs/reference/kubectl/cheatsheet/


## For later/ remember 
// Container runtime interface (CRI)

// Runtime Classes
