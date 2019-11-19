
# Kubernetes Exercises

Some exercises to get a quick look into Kubernetes!

## 0. Optional, setup a local cluster

See the [setup-local-cluster.md](setup-local-cluster.md) for the instructions how to setup a dev cluster with Kind on your workstation.

## 1. Practice with K8S

### Prerequisite

First and foremost, setup the kubectl cli on your shell, instructions can be found here:
https://kubernetes.io/docs/tasks/tools/install-kubectl/

### Your first deployment

In the deployments folder are several files present, each name corresponding with the k8s api object. We start with the deployment.yml, a definition for a simple hello pod. It serves a simple Nginx page which should look like something this:

```
Server address: 172.17.0.6:80
Server name: 5075daa7e4fc
Date: 15/Nov/2019:15:34:17 +0000
URI: /yolo
Request ID: b9166c7a11f0ff5294b376958618cd69
```

The task is to deploy this container to the cluster! The deployment is not complete so it is up to you to deploy it to your namespace and add other missing values.
The Kubernetes documents are a good reference for what exactly is missing:
https://kubernetes.io/docs/concepts/services-networking/service/

You can deploy object the cluster with the `kubectl apply` command, the `-f` flag allows pointing to a file to deploy. 

`kubectl apply -f deployments/deployment.yml`

After successful deploying you can check the status of the pod by running:

`kubectl get pods -n <namespace> --selector app.kubernetes.io/name=hello-world`

### Service

Now that the pod is running, we want to view this webpage, if you run the command above with the `-o wide` the Pod IP is provided. This Pod IP is an in cluster IP and not accessible from the outside.
To check that the pod is doing what is should we can deploy a test pod to the cluster.

In the deployments folder there is a shell-pod.yml file. This describes a pod which will do nothing but sleep for 1000000 seconds. This is needed for the pod to stay within the cluster, as soon as a process stops the container is restarted by Kubernetes. By starting a sleep process the container will not exit and allows us to gain access to the shell.

This might sound abstract but give it a try, comment out the args of the container and you will see it getting created, going into Completed/CrashLoopBackOff status and the restarts counter going up.

`kubectl apply -f deployments/shell-pod.yml`

Now redeploy the pod with the args and the pod will stay running for 1000000 seconds.
Since this pod is running, we can use it to gain a shell within the cluster:

`kubectl exec -it -n <namespace> shell -- /bin/bash`

Now since this pod runs within the cluster we can access the hello-world pod by Pod IP.

`curl <pod-ip>`

Cool, the pod works!

Now we want to unleash the true power of Kubernetes and increase the replicas of the hello-world pod. Go and edit the deployment.yml and increase the replicas to two and redeploy (you should know how by now ;)).

Now when you check the cluster state there should be two hello-world pods:

`kubectl get pods -n <namespace>`

When you go back to the shell of the shell pod you should be able to curl both pod ips.
But this is annoying, you have two replicas but not a single endpoint to access them. This is where the Service comes into play. This is the Kubernetes object that autodiscovers pod and add them to one endpoint.

The service deployment is located in deployments/service.yml and some values are missing, just like before.
Again, the Kubernetes documentation is a good start to find what is missing:

https://kubernetes.io/docs/concepts/services-networking/service/

As soon as the service is deployed you can see the Endpoint pointing to the pod ips and the service has a Cluster IP assigned. A Cluster IP is a static IP where the scaled pods are accessible on,

`kubectl get -n <namespace> enpoints`

`kubectl get -n <namespace> service`

Now from within the shell pod you can access the service Cluster IP and you will see the Server Name change since the requests are loadbalanced across the replicas of the hello-world pod.

### Ingress

Now that we have our hello-world pod replicated and behind a static Cluster IP it is ready for access from the outside world. 
This is done by creating a Ingress object which gets picked up by the Ingress controller within the cluster, Traefik in our case. As soon as Traefik discovers a new Ingress it exposes the service on the given URl with a default certificate.

The ingress deployment file is located under deployments/ and has some parameters missing and (should be of no surprise haha) the Kubernetes documents are a good start:

https://kubernetes.io/docs/concepts/services-networking/ingress/

You have the k8s-demo.intermax.io domain to chose a subdomain for, example: yolo.k8s-demo.intermax.io.
Be creative since these Ingress objects are clusterwide and must be unique :)

When the Ingress object is deployed successfully you can access the hello world pod by pointing your browser to the created url but add port 30443 (since kube-api is listening on 443):

https://yolo.k8s-demo.intermax.io:30443

### Choas Engineering

For more insight into Choas Engineering see the [this cool article](https://www.gremlin.com/community/tutorials/chaos-engineering-the-history-principles-and-practice/).

Since the pod is replicated the url should stay up even when one of the pods is killed. So go ahead kill a pod, first lookup the podname:

`kubectl get pods -n <namespace> --selector app.kubernetes.io/name=hello-world`

While refreshing the webpage go ahead and delete one of the pods by podname:

`kubectl delete pods -n <namespace> <podname>`

Now the Server Name does not change anymore since all the requests are loadbalanced to the same pod. As soon as the second pod is up again the Server Name will alternate between the two pods again.

## References

* [Understanding Kubernetes Objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)
* [Service](https://kubernetes.io/docs/concepts/services-networking/service/)
* [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)
