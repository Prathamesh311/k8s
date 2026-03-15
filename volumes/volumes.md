## Persistent Volumes (PV)

- Kubernetes Persistent Volumes (PV) provide a way to manage durable storage for applications running in a Kubernetes cluster.
- Unlike ephemeral storage tied to the lifecycle of a Pod.
- Persistent Volumes exist independently of Pods and remain intact even after Pods are deleted.
- This makes PVs ideal for stateful applications that require persistent storage, such as databases.

Persistent means permanent storage.

#### Key points:

- PVs are independent resources.
- They exist even if no Pod is using them.
- They are created by an administrator or dynamically by a StorageClass.
- Once a PV is created, it can be bound to a Persistent Volume Claim (PVC).

When a Pod requests storage through PVC, Kubernetes searches for a suitable PV to satisfy the request.

#### If no suitable PV is found:

- Kubernetes dynamically creates one (if the storage class supports dynamic provisioning)
- Otherwise the PVC remains unbound

Relationship:

```bash
Pod → PVC → PV
```

- PV maintains the total storage capacity, and PVC requests storage from it.

```bash
Component	       Purpose
  PV	        -   Admin provisioned storage
  PVC	        -   User request for storage
  Pod	        -   Uses the PVC to mount the PV
```
### Stateless

- If a Pod is deleted, the data is lost because the data is stored locally on the Pod or instance.

### Stateful

- If a Pod is deleted, the data remains persistent because the data can be stored in external storage such as AWS EBS.


## Persistent Volume Claim (PVC)

- To use a PV, we need to claim the volume using PVC.

PVC requests a PV with the desired specifications such as:

- Size
- Access modes
- Storage class
- Performance


- Once a suitable PV is found, it is bound to the PVC.
- After binding, the Pod can mount the PVC as a volume.
- When the user finishes using the storage, the attached PV can be released and reused.

If storage is created directly inside the cluster and the cluster is deleted, the storage will also be deleted.
Therefore external storage such as AWS EBS volumes is used.

---

#### Create Persistent Volume

- refer to pv.yml

```bash
kubectl create -f pv.yml
```

```bash
kubectl get pv
# check pv
```

#### Create Persistent Volume Claim

- refer to pvc.yml

```bash
kubbectl create -f pvc.yml
```

- Check PV and PVC:
```bash
kubectl get pv
kubectl get pvc
# PVC may show Pending state until a Pod consumes it.
```

- Check events:
```bash
kubectl get events
```

- Now lets setup a statefull application, statefull meaning, it will keep the previous data.

### What is a Kubernetes StatefulSet ?

- A StatefulSet is a Kubernetes workload API object used to manage stateful applications.
- Unlike Deployments or ReplicaSets, which manage stateless pods, StatefulSets are designed for apps that require stable identities, persistent storage, and ordered deployment.

- refer to deploy.yml

- Create deployment:

```bash
kubectl create -f deploy.yml
```

- Check pods:

```bash
kubectl get pods
```

- Verify Persistent Storage

```bash
# Access container:
kubectl exec -it <pod-id> -- /bin/bash
```

- Navigate to volume:

```bash
cd /tmp/persistent
```

- Create files:

```bash
touch file{1..5}
```

- Edit file:

```bash
vi file1
# Example content:
this is from pod-1 pv
```

- Exit container.

- Delete pod:

```bash
kubectl delete pod <pod-id>
```

- A new pod will be created automatically.

- Check data again:

```bash
kubectl exec -it <new-pod-id> -- ls /tmp/persistent
```

- The same files will still exist.

This demonstrates stateful behavior.

---

## Dynamic Provisioning

- A StorageClass is a way to dynamically provision PersistentVolumes (PVs) in Kubernetes without manually creating them ahead of time.

When you create a PersistentVolumeClaim (PVC) that refers to a StorageClass, Kubernetes:

- Automatically creates an EBS (or other backend) volume
- Binds that volume to your PVC
- Mounts it to your Pod

To use EBS-backed dynamic PersistentVolumeClaim (PVC) in a KOPS-managed Kubernetes cluster on AWS, using the built-in gp2 or gp3 StorageClass provided by AWS and KOPS.

- Note: If you directly create pvc without pv ... KOPS will create pv automatically. pv meaning , kops will create a EBS volume automatically.
- Note:  dynamic provisioning is enabled by default in KOPS

Verify StorageClass

```bash
kubectl get storageclass
```

- Create a PVC  -- no need to create pv. pv(ebs volume) will create automatically.

```bash
refer to ebs-pvc.yml
```

```bash
kubectl apply -f ebs-pvc.yml
```

- Pod Using the PVC

```bash
refer to ebs-pod.yml
```

```bash
kubectl apply -f ebs-pod.yml
# create ebs-pod.
```

```bash
# check pvc and pv
kubectl get pvc
kubectl get pv
```

```bash
kubectl get pod ebs-nginx
```

- You should see that the PVC is Bound, and a PV is dynamically created.
- You can also check EBS volumes in AWS Console → EC2 → Volumes — it will show as "in-use".

- Delete PVC (PersistentVolumeClaim)

```bash
kubectl delete pvc my-pvc -n default
```

- Delete PV (PersistentVolume)

```bash
kubectl delete pv my-pv
```

---

