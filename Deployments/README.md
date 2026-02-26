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

## Deployment Strategies

A Deployment in Kubernetes supports 2 main strategies:

## Rolling Update (Default Strategy)

- In Rolling Update, pods are replaced one by one.
- When the application is updated, Kubernetes deletes one old pod and creates one new pod at the same time.
- Both old and new versions run together during the update process.
- This ensures that the application remains available with almost no downtime.

- For example, if there are 3 replicas, Kubernetes will update them one by one until all pods are running the new version.
  This is the default strategy in Kubernetes Deployments.

## Recreate Strategy

- In Recreate strategy, Kubernetes deletes all existing pods first.
- After deleting them, it creates new pods with the updated version.
- During this time, the application becomes unavailable.

- This strategy is used only when the old and new versions cannot run together, such as in cases of major compatibility changes or  database schema updates.

## Blue-Green

- Blue-Green is not a built-in Deployment strategy in Kubernetes, but a release pattern.
- Two versions run separately -> Blue (old) and Green (new).
- Traffic is switched to the new version only after testing.
- It allows quick rollback with no downtime.

## Canary Deployment

- Canary is also not a default Kubernetes strategy, but a deployment approach.
- The new version is released to a small number of users first.
- Traffic is increased gradually after monitoring.
- If issues occur, the rollout can be stopped immediately.

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
kubectl get deploy -o wide 
```

```bash
kubectl get rs 
#Deployment will create replicaset 
```


```bash
kubectl get pods 
``` 
list Pods

```bash
kubectl describe deployment nginx-deployment
```  
Deployment details

```bash
kubectl delete deployment nginx-deployment
# deleted the nginx-deployment.
```

---

## Scale-out

```bash
kubectl scale deploy/ib-deployment --replicas=10
```
## Scale-in

```bash
kubectl scale deploy/ib-deployment --replicas=3
# What ever Replica set is doing ,Deployment is also doing the same thing .if you delete any pod, it will create automatically.
```

check by deleteing the pod 
```bash
kubectl delete pod pod-id
```

```bash
kubectl describe pod -l app=bank | grep -i ID 
# OR
kubectl describe pods | grep -i image   
# It shows all internet bankingrepo images.
```
Now what Deployment do additional thing is to change the application.

```bash
# First watch pods in another terminal
kubectl get pod --watch
```

```bash
kubectl edit deploy/ib-deployment   
# change to mobile banking image.
```
watch pods in another terminal , first it will create a new pods and then terminate old ones.

```bash
kubectl describe pods | grep -i image 
# Now you can see  mobilebanking image
```

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
# kubectl get events → Shows all events happening inside the cluster.
# --sort-by=.metadata.creationTimestamp → Sorts the events from oldest to newest based on when they were created.
```

---

## Rolling Update Example

```bash
kubectl rollout history deploy/ib-deployment 
# Used to check previous versions of a Deployment.
```

```bash
kubectl rollout undo deploy/ib-deployment   
# It will go back to previous application / image
```

```bash
kubectl get pods  
# Pods are terminating and creating new pods with new image, this was not possible in ReplicaSet.
```

```bash
kubectl describe pods | grep -i image
```

```bash
kubectl rollout pause deploy/ib-deployment  
# it is like lock, cannot undo, cannot rollout to previous version
```

```bash
kubectl rollout undo deploy/ib-deployment  
# Not possible because we have pause the deployment , to do this undo first we need to resume the deployment.
```

```bash
kubectl rollout resume deploy/ib-deployment 
```

```bash
kubectl rollout undo deploy/ib-deployment 
# now it is possible
```

```bash
kubectl rollout status deploy/ib-deployment
# Used to check live rollout progress
```


---
