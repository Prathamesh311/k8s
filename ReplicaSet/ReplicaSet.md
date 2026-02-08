## ReplicaSet

A ReplicaSet ensures that a specified number of pod replicas are running at any given time.

If a pod fails or is deleted, the ReplicaSet automatically creates a new pod to maintain the desired state.

ReplicaSet provides self-healing and high availability for pods.

In real-world usage, ReplicaSets are usually managed by Deployments, not created directly.


---

## Labels

As we are creating multiple pods with same application, all these pods have different names but how to group all of them as we have 1 application with multiple pods. So we can give a label to group them.

Individual pods are difficult to manage because they have different names so we can give a common label to group them and work with them together.



---

## Selectors

It is used to select pods with same labels.

Selectors are used by ReplicaSet to identify and manage the correct pods.

ReplicaSet uses selectors to continuously watch pods with matching labels and ensure the desired number of replicas are running.



---

## Commands and use-case

```bash
kubectl get rs -o wide
```
This command will get more details about ReplciaSets.



```bash
kubectl describe rs nginx-replicaset
```
This will describe about nginx-replicaset.



```bash
kubectl get pods --show-labels
```
This will list the pods with Labels.


```bash
#If you delete any pod, automatically new pod will be created, if you want to watch live, open another terminal and use this
kubectl get pods --watch
```


```bash
kubectl delete pods pod-id
```
First new pod creates and the existing pod will be deleted.
New pod got created, this is called ReplicaSet, if one pod delete , another pod will get created automatically.


```bash
kubectl get pods -l app=nginx
```
This will list all the pods with label nginx, l = label  

```bash
kubectl delete pods -l app=nginx
```
To delete all the pods with label nginx.


---

## SCALE REPLICAS - Scale Out and Scale In

## Scale Out

First open another window live
```bash
kubectl scale rs/nginx-replicaset --replicas=10 
```
Now see pods are creating live.

## Scale In

```bash
kubectl scale rs/nginx-replicaset --replicas=5
``` 


LIFO: LAST IN FIRST OUT.

If a Pod is Created lastly it will delete first when scale in happens.

The Scale out and Scale in is the manual process.


---

Now, all pods are running with nginx image , but if i want to change the image to another image and update the POD, this not possible in ReplicaSet.

```bash
kubectl describe pod -l app=nginx | grep -i ID
# All pods are using nginx
```

For Updating the image in the replicaset, you cannot update in yml file, it will create a new replicaSet
so there is a command to edit current replicaset.

```bash
kubectl edit rs/nginx-replicaset
# change nginx to another image
```

```bash
kubectl describe pod -l app=nginx | grep -i ID
```
Still it will show nginx, image is not change , that's the problem with ReplicaSet, We cannot update the application


---

```bash
vi replicaset.yml
# Now change the image. 
```

```bash
kubectl apply -f replicaset.yml
```
This will give error that nginx-replicaset already exits. So we need to create a new replicaset again.


```bash
kubectl get pods --show-labels
```

```bash
kubectl describe pod -l app=nginx | grep -i ID 
# After updating the image ,you will still see old image as nginx 
```
But if you scale out, new pods will contains that updated image.

```bash
kubectl scale rs/nginx-replicaset --replicas=5
```

```bash
kubectl describe pod -l app=nginx | grep -i ID
# If we scale the ReplicaSet, only new pods will use the updated image.
```

## This is the drawback of replicaset. Using ReplicaSet we cannot roll out the application.

### Advantages:

self healing
scaling

### Drawbacks:

we cannot roll in and roll out and also we can't update the applications using ReplicaSet.


