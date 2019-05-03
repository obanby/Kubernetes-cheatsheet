# Learning Kubernetes
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

Kubernetes cheat sheet
https://kubernetes.io/docs/reference/kubectl/cheatsheet/
