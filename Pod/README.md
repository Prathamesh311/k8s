# Pod

Pods are the smallest and simplest Kubernetes objects.

A pod represents a single instance of a running process in your cluster and can contain one or more containers.

Pods are ephemeral, meaning they can be created and destroyed as needed, and are the unit of scaling in Kubernetes.

This example runs an Nginx container inside a Pod.

---

## Commands and Use Case

```bash
kubectl apply -f pod.yaml
```
Creates the pod from YAML file.


```bash
kubectl get pods
```
Shows all running pods.


```bash
kubectl describe pod nginx-pod
```
Shows detailed information about the pod.


```bash
kubectl logs nginx-pod
```
Shows logs of the container.


```bash
kubectl delete pod nginx-pod
```
Deletes the pod.