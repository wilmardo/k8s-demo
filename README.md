# k8s-demo

The purpose of this demo is to get a quick insight into Kubernetes and the Kubernetes featureset.

## Introduction

Kubernetes is a container orchestration engine and works with several container runtimes (not just Docker!), see the Kubernetes [docs](https://kubernetes.io/docs/setup/production-environment/container-runtimes/) for more information.

Kubernetes works with YAML declations to define the cluster state. YAML is a subset of JSON and designed to be more human readable and workable then JSON. More information about YAML can be found [in this article](https://dev.to/paulasantamaria/introduction-to-yaml-125f). Kubernetes YAML files will get converted to JSON by kubectl before sending to the API ;)

Kubernetes is build API first and object orientated. All the declarions in YAML are based on API objects, with three major fields:

  * `apiVersion` - Which version of the Kubernetes API you’re using to create this object
  * `kind` - What kind of object you want to create
  * `metadata` - Data that helps uniquely identify the object, including a name string, UID, and optional namespace
  * `spec` - What state you desire for the object

An example of such a deployment

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: 
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

## Exercises

* Kubernetes - see [k8s/exercises.md](k8s/exercises.md)
* Docker - see [docker/exercises.md](docker/exercises.md)

---

## Kubernetes common command reference

### Overview

* `kubectl describe` - describe an object, it is like get but more descriptive (logging excerpt etc)
* `kubectl get` - get an object from the api
* `kubectl apply` - apply objects to the cluster
* `kubectl delete` - delete an object
* `kubectl exec` - execture a command to a running container

### Usefull examples

* `kubectl describe -n kube-system pod etcd-node01` - describe pod with name
* `kubectl describe -n kube-system pod --selector component=etcd` - describe pod with label selector, useful when a deployment exists of multiple pods

* `kubectl get pods --all-namespaces` - get all pods within the cluster
* `kubectl get -n kube-system pods` - get all pods within a namespace
* `kubectl get -n kube-system pod etcd-node01 -o yaml` - get yaml specification of an existing pod

* `kubectl apply -f deployment.yml` - apply an object specified in an yaml file to the cluster
* `kubectl apply -R -f deployments/` - recursively apply a directory of yaml files

* `kubectl delete -f deployment.yml` - delete an object specified in an yaml file from the cluster 
* `kubectl delete -n kube-system pod etcd-node01` - delete the etcd-node01 pod
* `kubectl delete -n kube-system pod -l app.kubernetes.io/name=hello-world` - delete all the pods with the app.kubernetes.io/name: hello-world label

* `kubectl exec -it -n kube-system etcd-node01 -- /bin/bash` - run a shell in the etcd-node01 pod

## Docker common command reference

### Overview

* `docker build` - build a Docker image
* `docker tag` - tag a Docker image
* `docker run` - run a Docker container

### Usefull examples

* `docker build -t intermaxcloudsourcing/hello-world:latest .` - build the Dockerfile in the current directory and tag the image with intermaxcloudsourcing/hello-world:latest
* `docker build -t intermaxcloudsourcing/hello-world:latest --no-cache .` - 

* `docker tag intermaxcloudsourcing/hello-world:latest intermaxcloudsourcing/hello-world:1.0.0` - tag image intermaxcloudsourcing/hello-world:latest to intermaxcloudsourcing/hello-world:1.0.0

* `docker run -it intermaxcloudsourcing/hello-world:latest` - run the intermaxcloudsourcing/hello-world:latest image with an interactive terminal
* `docker run -it -p 5000:5000 intermaxcloudsourcing/hello-world:latest` - run the intermaxcloudsourcing/hello-world:latest image with the current directory (pwd) mounted to /data in the container
* `docker run -it -v ./app:/data intermaxcloudsourcing/hello-world:latest` - run the intermaxcloudsourcing/hello-world:latest image with the data directory mounted to /data in the container
* `docker run -it -v $(pwd):/data intermaxcloudsourcing/hello-world:latest` - run the intermaxcloudsourcing/hello-world:latest image with the current directory (pwd) mounted to /data in the container
