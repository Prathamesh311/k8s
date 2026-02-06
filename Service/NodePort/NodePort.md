### NodePort Service

NodePort exposes an application outside the Kubernetes cluster.

This type of Service exposes the Service on each Node’s IP at a static port.

A NodePort Service is accessible from outside the cluster by hitting the <NodeIP>:<NodePort>.

When a NodePort is created, kube-proxy exposes a port in the range 30000-32767



---

### Commands and Use Case

This example exposes an Nginx application using NodePort Service.

```bash
kubectl apply -f service2.yml
```
```bash
kubectl describe svc nginx-nodeport
```
Shows detailed information about NodePort service.
```bash
minikube ip ----> 192.168.49.2
```
Shows Minikube node IP address.

```bash
minikube service nginx-nodeport
```
Opens NodePort service in browser.

```bash
http://192.168.49.2:30007
```
Access application using Node IP and NodePort.

```bash
kubectl delete -f service2.yml
```
removes the Service, Deployment , created using the same YAML file.

### Traffic Flow

Browser --> Minikube IP (192.168.49.2) --> Port 30007 (NodePort) --> Service --> Pod (container:80)

