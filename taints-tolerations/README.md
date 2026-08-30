### Taints and Tolerance

- Taints are like locks on the doors of your house, restricting access to certain pods (visitors).
- Tolerations are like keys that pods (visitors) can carry to bypass the locks (taints) and be scheduled on nodes.
- Example like Lock (taints) and Key (tolerance)


In Kubernetes, taints and tolerations work together to control the scheduling of Pods onto Nodes. Taints are applied to Nodes to prevent certain Pods from being scheduled on them, while tolerations are applied to Pods to allow them to be scheduled on Nodes with matching taints. ​

#### Apply a Taint to GPU Nodes

- First, taint the GPU Nodes to repel/force Pods that do not require GPU resources:

```bash
kubectl get nodes
```

```bash
kubectl taint nodes i-0333b24c25bf4868b hardware=gpu:NoSchedule
```

- This command adds a taint with key hardware, value gpu, and effect NoSchedule to the specified Node. As a result, Pods without a matching toleration will not be scheduled on this Node.

#### It contents 3 Effects

- NoSchedule: Pods will not be scheduled onto the tainted node unless they have a matching toleration.
- PreferNoSchedule: Scheduler tries to avoid scheduling pods onto the tainted node but can do so if necessary.
- NoExecute: Existing pods on the node without matching tolerations are evicted.

#### Note: Taints and Tolerance will not gurantee to have pod on the same node.

- Lets test:
```bash
vi deploy.yml
```
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 1
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
```
```bash
kubectl create -f deploy.yml
```
```bash
kubectl get pods -o wide  # all pods are scheudled on another node.
```

#### Add a Toleration to GPU-Requiring Pods.

- Next, add a toleration to the Pods that require GPU resources, allowing them to be scheduled on the tainted Nodes:

```bash
vi deploy-toleration.yml
```

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 1
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
      tolerations:
        - key: "hardware"
          operator: "Equal"
          value: "gpu"
          effect: "NoSchedule"
```

- In this Pod specification, the toleration matches the taint applied to the GPU Nodes, permitting the Pod to be scheduled on those Nodes.

#### Key Points:

- Taints are applied to Nodes to repel certain Pods. They consist of a key, value, and effect (NoSchedule, PreferNoSchedule, or NoExecute). ​

- Tolerations are applied to Pods to allow them to be scheduled on Nodes with matching taints. They must match the key, value, and effect of the taint to be effective.

