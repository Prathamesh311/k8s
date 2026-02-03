# Kubernetes Deployment

## What is a Deployment?

Kubernetes deployment is a high-level resource object by which you can manage the deployment and scaling of the applications while maintaining the desired state of the application. 

You can scale the containers with the help of Kubernetes deployment up and down depending on the incoming traffic. 

If you have to performed any rolling updates with the help of deployment and after some time if you find any bugs in it then you can perform rollback also.

Kubernetes deployments are deployed with the help of CLI like Kubectl it can be installed on any platform.

---

## Why Deployment is Used
- Runs multiple replicas of Pods
- Auto recreates Pods if they crash
- Supports rolling updates
- Supports rollback

---

## What Deployment Manages

Deployment
 → ReplicaSet
   → Pods
     → Containers

---

## Commands Used

```bash
kubectl create -f deployment.yaml
```  
Create deployment from yml file

```bash
kubectl get deployments
```  
List deployments

```bash
kubectl get pods 
``` 
list Pods

```bash
kubectl describe deployment nginx-deployment
```  
Deployment details

---

## Rolling Update Example

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.26 
``` 
Updating container image and then apply it.

```bash
kubectl apply -f Deployment.yaml
```

---

## Check Rolling Update Status

```bash
kubectl rollout status deployment nginx-deployment
```  
Shows update progress

```bash
kubectl get pods 
``` 
Old pods terminates, new pods gets started.

---

## Rollback 

```bash
kubectl rollout undo deployment nginx-deployment
```
Rolling back to Previous version.

```bash
kubectl get pods
```

```bash
kubectl delete deployment nginx-deployment
```  
Delete deployment


---