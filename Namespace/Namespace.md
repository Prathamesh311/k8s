# Namespace

A Namespace is a logical partition inside a Kubernetes cluster used to organize and isolate resources.

Namespaces allow multiple teams, applications, or environments to share the same cluster without interfering with each other.



---
## TYPES:

default : This is default namespace, all objects are created here
kube-node-release : It will store the object which is taken from one namespace to another
kube-public : All Public objects are stored here, generally namespace are private.
kube-system : By default K8S will create some object, those are stored here.

Every component of kubernetes cluster is going to create in the form of POD, All these PODS are stored in Kube-System Namespace.


---

## Command use-case

```bash
kubectl get ns
#Shows all namespaces in the cluster.
```

```bash
kubectl get pods -A
#Lists pods from all namespaces.
```

```bash
kubectl create ns dev
#Creates a namespace called dev.
```

```bash
kubectl get ns
#Verifies namespace creation.
```

```bash
kubectl config view
#If no namespace is shown, you are in the default namespace.
```

```bash
kubectl config set-context --current --namespace=dev
#Switches current context to dev namespace.
```

```bash
kubectl config view
#Now the output shows dev as the active namespace.
```

```bash
#creating some pods in dev namespace
kubectl run dev1 --image=nginx
kubectl run dev2 --image=nginx
kubectl run dev3 --image=nginx
```

```bash
#Viewing Pods Across Namespaces
kubectl get pods -n prod
```

```bash
kubectl delete pod dev1 -n dev
#Deletes pod dev1 from dev namespace.
```

```bash
kubectl delete namespace <namespace-name>
#Deletes the namespace and all resources inside it.
```

---


## Important Security Note

In this case, anyone can access/delete/create pods in any namespace. Which is not good. for this we need to restrict users to access namespaces using RBAC(Role-Based Access Control).

---

## Why Namespace is Needed

Without namespaces:
- All resources live in the same space
- Resource name conflicts occur
- No isolation between teams or environments
- Hard to manage access control

Namespaces solve:
- Resource isolation
- Environment separation (dev, qa, prod)
- Better organization
- Access control using RBAC

---


