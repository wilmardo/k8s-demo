# Setup a local Kubernetes cluster with Kind

When you have no external cluster available the easiest way to get started locally is with [Kind](https://github.com/kubernetes-sigs/kind). The installation instructions can be found in the [README.md](https://github.com/kubernetes-sigs/kind#installation-and-usage) of the Kind repo.
You can setup a master/worker cluster with the following command:
```
kind create cluster --config kind-config.yml
```

When the cluster is created point kubectl to the kind kubeconfig:

```
export KUBECONFIG="$(kind get kubeconfig-path --name="kind")"
```

Test your access to the cluster with:

```
kubectl cluster-info
``` 

When running into issues with Kind, refer to their [Known Issues](https://kind.sigs.k8s.io/docs/user/known-issues/) page for common issues and their solutions.
