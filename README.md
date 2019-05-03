# Learning Kubernetes

Running a particular image on a cluster
`kubectl run hello-minkube --image=gcr.io/google_containers/echoserver:1.4 --port=8080`

Expose this port to the host
`kubectl expose deployment hello-minkube --type=NodePort`

`minikube service hello-minkube --url`

Before we can launch a container based on an image we have to create a pod definition
`kubectl create -f pod-goAndNode.yaml`


## Useful commands
Get information about all running pods
`kubectl get pod`

Describe one pod
`kubectl describe pod <podName>`

Export the port of a pod (create a new service)
`kubectl expose pod <podName> --port=<portNumber> --name=<name>`

Port forward that expose pod port to your local machine
`kuberctl port-forward <podName> <portNumber>`

Attach a pod
`kubectl attach <podName>`

Execute commands on a pod
`kuberctl exec <podName> -- <commands>`

Add label to pod after they are created
`kubectl label pods <podName> mylabel=<newLabel>`

Run a shell in a pod
`kubectl run -i --tty busybox --image=busybox`
