# Exploit

## Create cluster
```
minikube start
```

## Check allocatable CPU
```
kubectl get nodes
kubectl describe node minikube
```

The cluster created by minikube just has one node. Under the `Allocatable` section, we can see that this node has 4 CPU cores.

## Create virtuous deploy
Create a virtuous deployment and check all pods are up and running:

```
kubectl apply -f virtuous-deploy.yaml
kubectl get pods --all-namespaces
```

## Explore

```
kubectl get priorityclass
```

There are two by default:
- `system-cluster-critical` with value `2000000000`.
- `system-node-critical` with value `2000001000`.

Let's see the pods we have in the cluster, their priority class, priority, and CPU requests:

```
kubectl get pods --all-namespaces -o custom-columns=POD:.metadata.name,PRIORITY_CLASS:.spec.priorityClassName,PRIORITY:.spec.priority,CPU_REQUESTS:'.spec.containers[*].resources.requests.cpu'
```

## Create evil deploy

It's time to exploit! 

```
kubectl apply -f evil-deploy.yaml
kubectl get pods --all-namespaces
```

We should see the evil pod running and two pods preempted and pending.

## Clean up
minikube delete
