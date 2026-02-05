# ClusterIP Service

This is the default type of Service.

It exposes application only inside the cluster.

ClusterIP is mainly used for communication between Pods.

It exposes the Service on a cluster-internal IP. Other services within the same Kubernetes cluster can access the Service, but it is not accessible from outside the cluster.

---

# Commands 

```bash
kubectl create -f service1.yml
```
Creates the ClusterIP service from YAML file.

```bash
kubectl get svc nginx-clusterip
```
Shows specific service details like Type,Port,ClusterIP, ExternalIP.

```bash
kubectl describe svc nginx-clusterip
```
Shows detailed information about the service.
