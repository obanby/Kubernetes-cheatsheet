# Learning Kubernetes
## About

This repo is intended to be my personal studying notes for k8s. It'll continue growing with time. I'll try my best to document all resources where I got the information from.

## Kubernetes
It is basically a cluster for running apps and an orchestrator of Cloud native micro-services apps

### K8S Components

#### Fundamental concepts:
  - Desired state
  - Current state
  - Declarative model

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

### kubectl

> It's the main k8s CLI tool. It converts user-friendly commands into JSON payload required by the API server. It uses configuration file  to know which cluster and API server endpoints to POST to.
> - The kubernetes book 2019

It lives `~/.kube/config` it contains definition for
  - clusters:
    * name
    * certificate
    * API end point
  - users
    * name
    * username
    * credentials
  - contexts
    * cluster
    * user

### Pods

  - Api version
  - kind
  - metadata
  - spec

### Deployments
  - Deployments manage replica-sets, replica-sets manages pods

  - apiVersion
  - kind
  - metadata (name, labels)
  - spec (Anything below here relates to the pod)
  - spec.template.spec (Anything below here relates to the container)

## Useful commands

To find out about an object structure

`kubectl explain <object>`

To find our more about the object structure

`kubectl explain <pod.spec>`

To view kubectl config

`kubectl config view`

Getting kubectl context

`kubectl config get-contexts`

To choose a context

`kubectl config get-contexts <context-name>`

To check current context

`kubectl config current-context`

To create a deployment from a file

`kubectl create -f <file-name>`

To create objects from folders

`kubectl create -f <folder-path>`

To make changes to a deployment/services within a file

`kubectl apply -f <file-name>`

To create an object from a url

`kubectl apply -f <url>`

Running a particular image on a cluster

`kubectl run hello-minkube --image=gcr.io/google_containers/echoserver:1.4 --port=8080`

To scale a deployment

`kubectl scale --replicas=<number-of-replicas> <deploymentname>`

To view deployments with more info

`kubectl get deployments -o wide`

To check all the name spaces

`kubect get namespaces`

N.B (Default name space is used by default! also k8s uses kube-system & kube-public behind the scenes so don't deploy there)

To run kubectl commands aganinst any other name space but the default

`kubectl <command> -n <name-space>`

To get objects in all name spaces

`kubectl <command> --all-namespaces`

To see everything you have

`kubectl get all`

To check pods labels

`kubectl get pods --show-labels`

To add a label after deployment

`kubectl label pod/<pod-name> <label>=<value> --overwrite`

To delete a label after deployment

`kubectl label pod/<pod-name> <key>-`

To search with labels

`kubectl get pods --selector <key>=<value-of-search>`

To search with multilabels

`kubectl get pods --selector <key>=<value-of-search>,<key>=<value-of-search>`

To search for unequality

`kubectl get pods --selector <key>!=<value-of-search>`

To search using the in operator

`kubectl get pods -l  '<key> in (1.2,2.0)'`

N.B: you can use -l instead of --selector ;)

To search using the notin operator

`kubectl get pods -l  '<key> notin (1.2,2.0)'`

To delete using labels

`kubectl delete pods -l <key>=<valu-of-search>`

To sort your objects while inspecting

`kubectl get <object> --sort-by=<query! ex: .metadata.name>`

To watch an object in k8s

`kubectl get <object> -w`

Expose this port to the host

NodePort: Allows us to have external ip address connected to the port that we specified with the kubectl run  

`kubectl expose deployment hello-minkube --type=NodePort`

`minikube service hello-minkube --url`

Before we can launch a container based on an image we have to create a pod definition

`kubectl create -f pod-goAndNode.yaml`

To delete a deployment

`kubectl delete deployment <deployment-name>`

To delete all objects of a kind with one command

`kubectl delete <object> --all`

N.B: Be catious it will wipe it!!

Feeling more adventrous with your deleteing!! Force it

`kubectl delete <object> --all --grace-period=0 --force`

To delete with a label

`kubectl delete <object> -l <label ex env=staging>`

To apply configs from file to cluster

`kubectl apply -f <file-path.yaml/json>`

To create a configmap from an env file

`kubectl create configmap game-config-env-file \
       --from-env-file=<file>.properties`

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

OR:

kubectl exec <pod-name> env


To get cluster information

`kubectl cluster-info`

To get container logs

`kubectl logs <pod-name>`

To get a logs for a previous conatiner which got restarted

`kubectl log <pod-name> --previous`

N.B: When looking on the exit code for a restarted container. Look for the Exit code section which is 128 + x = ExitCode

To delete a replica controller without deleting the pods it manages

`kubectl delete rc <rc-name> --cascade=false`

## Resources
1- The Kubernetes Book 2019
2- Kubernetes cheat sheet https://kubernetes.io/docs/reference/kubectl/cheatsheet/
3- Kubernetes Native tools (course)

## For later/ remember
// Container runtime interface (CRI)

// Runtime Classes

## Useful Tools

1- Helm    (Package mager)
2- Kompose (Docker compose to k8s objects)
3- kubedef (sync across clusters)
4- kubeadmin (Ease the k8s installation process)


## Liveness probe

There are three types of livness probe avialable on k8s:

1- http get probe (sends a get request to an end point)
2- TCP socket connection
3- Exec probe (will execute commands inside the container and checks the status code returned)


```
livnessProbe:
  httpGet:
    path: /<anyEndPoint>
    port: <anyPort>
```

## Some notes

k8s command is == Docker ENTRYPOINT // the executable that's executed inside the container
k8s args is === Docker CMD // the arguments passed to the executable

```
 spec:
  containers:
  - image: luksa/fortune:args
  args: ["2"]
```

> Like the container’s command and arguments, the list of environment variables also cannot be updated after the pod is created.

```
 spec:
  containers:
  - image: luksa/fortune:env
    env:
    - name: INTERVAL
      value: "30"
```

To refrence an existing enviroment variable, make sure you use $()

```
env:
- name: host
  value: "fakesite"
- name: url
  value: "http://$(host):8080"
```

## Config maps

`kubectl create configmap`

```
$ kubectl create configmap <configmap-name> --from-literal=<key>=<value>
configmap "fortune-config" created
```

To describe a config map in a yaml format
`kubectl get configmap <configmap-name> -o yaml`

To create a configmap from a config file or .env file
`kubectl create configmap <name> --from-file=./.env`

To nest all configuration from a file under a custom key
`kubectl create configmap <name> --from-file=<custom-key>=<file-path>`

To create all configuration from a directory and use their file name as key
`kubectl create configmap <name> --from-file=<custom-key>=<dir-path>`

# API

To get information about the cluster master
`kubectl cluster-info`

To run proxy (to let k8s handle authentication)
`kubectl proxy`

N.B: > k8s api is on port 8001

To list all your end points for k8s api

```
// in separate shell run
kubectl proxy

// in the other shell
curl http://localhost:8001/
```

To enable REST API UI with swagger, run the api server with
`--enable-swagger-ui=true`
