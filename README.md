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

See [exercises.md](exercises.md) in the root of this repository.

---

## Kubernetes common command reference

### Overview

* `kubectl describe` - describe an object, it is like get but more descriptive (logging excerpt etc)
* `kubectl get` - get an object from the api
* `kubectl apply` - apply resources to the cluster
* `kubectl exec` - execture a command to a running container

### Usefull examples

* `kubectl describe -n kube-system pod etcd-node01` - describe pod with name
* `kubectl describe -n kube-system pod --selector component=etcd` - describe pod with label selector, useful when a deployment exists of multiple pods

* `kubectl get pods --all-namespaces` - get all pods within the cluster
* `kubectl get -n kube-system pods` - get all pods within a namespace
* `kubectl get -n kube-system pod etcd-node01 -o yaml` - get yaml specification of an existing pod

* `kubectl apply -f deployment.yml` - apply a yaml file to the cluster
* `kubectl apply -R -f deployments/` - recursively apply a directory of yaml files

### References

* [Understanding Kubernetes Objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)
* [Service](https://kubernetes.io/docs/concepts/services-networking/service/)
* [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)
