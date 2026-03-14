## Resource Quota

- Kubernetes cluster can be divided into namespaces.
- By default, the pods in Kubernetes will run with no limitation of CPU and Memory.
- We need to give limits for the pods.

### ResourceQuota can limit:

- The number of objects that can be created in a namespace
- The total amount of resources used inside a namespace

The Pod Scheduler in the master node checks the worker nodes for CPU and Memory availability and then schedules the Pod.

#### We can set limits to:

- CPU
- Memory
- Storage

CPU is measured in cores and memory is measured in bytes.

```bash
1 CPU = 1000 milliCPUs
```

- Requests and Limits

```bash
Requests = How much resource we want
Limits   = Maximum resource we want
```

- Limits can be given to Pods and Nodes.
- The default limit is 0.

#### Rules:

If you mention request and limit, everything works fine.
If you do not mention request but mention limit, then

```bash
Request = Limit
```
If you do not mention request and limit, then
```bash
Request != Limit
```

- Every Pod in the namespace must have a CPU limit.
- If CPU is not mentioned, the Pod can consume all CPU resources.
- The total CPU used by all Pods inside the namespace must not exceed the specified limit.

---

```bash
kubectl create ns dev
```

```bash
kubectl config set-context --current --namespace=dev
```

```bash
kubectl config view  
# To see which namespace we are using
```

refer to dev-quota.yml

- Pods: A maximum of 5 pods can be created in dev namespace.​
- CPU Limits: The total CPU limit across all containers is restricted to 1 CPU.​
- Memory Limits: The total memory limit across all containers is capped at 1 GiB.

```bash
kubectl create -f dev-quota.yml
```

```bash
kubectl get quota
# To get the quota info.
```

```bash
NAME        AGE   REQUEST     LIMIT
dev-quota   69s   pods: 0/5   limits.cpu: 0/1, limits.memory: 0/1Gi
```

```bash
kubectl run pod1 --image=nginx  
# This will not work because, we need to mention quota for dev namespace as we are in that namespace.
# we can put --cpu and --memory in the command or use manifest file.
```

- deploy1.yml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
        - name: cont1
          image: prathshingate/bank-app:ib
          resources:
            limits:
              cpu: "1"
              memory: 512Mi
```

```bash
kubectl create -f deploy1.yml
```

```bash
kubectl get pods  
# It has created only 1 pod, as we mention replica = 3 , this is due to restriction we made for this namespace dev.
```

- In the above manifest file, we are mentioning cpu 1 and memory 512 for each pod, but in namespace dev we have restricted to 1cpu and 1GB memory
- if you run kubectl create -f deploy1.yml , it will create only 1 pod because of restriction.

```bash
kubectl delete -f deploy1.yml
```

- If you want all 3 pods put cpu as 0.3 ad 300Mi, it will create 3 pods.

refer to deploy2.yml

```bash
kubectl create -f deploy2.yml
```

```bash
kubectl get pods
```

```bash
kubectl get quota
```

---