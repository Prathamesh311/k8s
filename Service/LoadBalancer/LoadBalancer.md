### LoadBalancer Service

This Service type exposes the Service externally using a cloud provider’s load balancer.

It is typically used in cloud environments like AWS, GCP, or Azure.

A LoadBalancer is a Kubernetes service that:
Creates a service like ClusterIP
Opens a port in every node like NodePort
Uses a LoadBalancer implementation from your cloud provider (your cloud provider needs to support this for LoadBalancers to work).

In local environments like Minikube, a real cloud load balancer is not available, so `minikube tunnel` is used to simulate it.

---

### How LoadBalancer Works

Client   --->   External IP (LoadBalancer)  --->   Service  --->   Pods

### Commands

```bash
kubectl create -f service3.yml
```
Creates the Deployment and LoadBalancer Service from the YAML file.

```bash
kubectl get svc
```
To get the service info

```bash
minikube tunnel
```
Creates a tunnel to simulate a cloud LoadBalancer in Minikube and assigns an external IP.

```bash
kubectl get endpoints nginx-loadbalancer
```
Shows which pods are connected to the LoadBalancer Service.

```bash
http://<EXTERNAL-IP>  
```
Accesses the application using the LoadBalancer external IP.

```bash
kubectl delete -f service3.yml
```

