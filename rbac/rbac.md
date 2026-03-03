
# Role-Based Access Control (RBAC)

- RBAC in Kubernetes is used to control who can perform what actions on which resources inside the cluster.

- RBAC helps you define what actions (verbs like get, list, create, delete, etc.) can be performed on specific resources (like pods, services, deployments, etc.)  within specific namespaces or across the cluster.

- RBAC in Kubernetes allows you to define roles and permissions for users, groups, or service accounts. It consists of four main components:

1. Role: A set of permissions (rules) that are defined within a namespace. Roles are used to grant access to resources within a specific namespace.

2. RoleBinding: Grants the permissions defined in a Role to a user or service account within a specific namespace.

3. ClusterRoles: Similar to roles, but apply across the entire cluster.

4. ClusterRoleBindings: Associate cluster roles with users, groups, or service accounts across the entire cluster.

- Authentication(who are you?) and Authorization (what can you do?)

---

## Users vs Service Accounts

- Before setting up RBAC, it’s important to understand the Kubernetes user model.
- There are two ways to create “users” depending on the type of access that’s required:

### Users 

- In Kubernetes, a User represents a human who authenticates to the cluster using an external service.
- You can use private keys (the default method), a list of usernames and passwords, or an OAuth service such as Google Accounts.
- Users are not managed by Kubernetes; there is no API object for them so you can’t create them using Kubectl. You must make changes at the external service provider.

### Service Accounts 

- Service Accounts are token values that can be used to grant access to namespaces in your cluster.
- They’re designed for the use of applications and system components. Unlike Users, Service Accounts are backed by Kubernetes objects and can be managed using    the API.

---

## Service Account RBAC in Kubernetes:

- Assume authentication has been done on the Kubernetes cluster by company with user jack, Let's give permissions to him on cluster to only get, list and watch.


- Create a namespace in Kubernetes
```bash
kubectl create ns dev 
```

```bash
kubectl config set-context --current --namespace=dev
```

- First create a sample serviceaccount here in kubernetes called jack

- Create a Serviceaccount ---> Create a ROLE(give permissions) --->  Role Binding

refer to serviceaccount.yml


```bash
kubectl create -f serviceaccount.yml
```

- Create a Role within the dev namespace that allows the user jack to perform certain actions on pods:

refer to role.yml

```bash
kubectl create -f role.yml
```

- Next, bind the pod-reader Role to a user or service account. For simplicity, we'll bind it to a service account named jack.

- This RoleBinding binds the pod-reader Role to the jack user, allowing that user to get, list, and watch pods in the dev namespace.

refer to rolebinding.yml

```bash
kubectl create -f rolebinding.yml
```

### Verifying Access

- To verify that the jack user has the correct permissions, you can impersonate the user using kubectl.

```bash
kubectl auth can-i list pods --namespace=dev --as=jack   
# answer yes
```

```bash
kubectl auth can-i create pods --namespace=dev --as=jack  
# answer no
```

```bash
kubectl get roles -n dev    
# List all roles in a namespace
```

```bash
kubectl describe rolebinding read-pods -n dev
```

---

## user-based RBAC in Kubernetes

-------------------------------------------------
| Component   | Purpose                          |
| ----------- | -------------------------------- |
| CSR         | Request user authentication      |
| Role        | Define permissions               |
| RoleBinding | Link role to a user              |
| Kubeconfig  | Store user credentials & context |


*.key → generated first (private key)

*.csr → created using the key / Signing Request

*.crt → Certificate


- Workflow

[Generate Private Key] ------> signs ------> [CSR] -----> sent to CA (verified or self signed)-----> [CRT issued]


Certificate Signing Request (CSR)

Delete if any existing ns called development

Note: you should not be in same namespace if you want to delete. Move out of it to another namespace like default and delete

-- kubectl config set-context --current --namespace=default
-- kubectl delete namespace <namespace-name>


Let’s say we have a Kubernetes namespace called “development” and we want to grant permissions to a user named “dev-user” to manage pods within that namespace.

#### Create a namespace called development

```bash
kubectl create ns development
```
```bash
kubectl config set-context --current --namespace=development
```

- Have some sample pods running in development namespace


vi deploynew.yml
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 4
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
kubectl create -f deploynew.yml
```
```bash
kubectl get pods
# To get pods
```


#### Scenario

- You want to create a user dev-user who:
  Can only list and get pods

- Works only in the development namespace


| Component      | Description                                       |
| -------------- | ------------------------------------------------- |
| `dev-user.key` | Private key                                       |
| `dev-user.csr` | Certificate Signing Request                       |
| `dev-user.crt` | User certificate issued by K8s CA                 |
| `kubeconfig`   | Lets user connect securely with restricted access |
| `Role`         | Grants specific permissions in a namespace        |
| `RoleBinding`  | Binds the user (CN) to the Role                   |




1. Create a Private Key and Certificate Signing Request (CSR)

```bash
openssl genrsa -out dev-user.key 2048    
# To create a Private Key
```

```bash
openssl req -new -key dev-user.key -out dev-user.csr -subj "/CN=dev-user/O=dev-group"   
# To create a CSR
```

2. Base64 Encode the CSR

```bash
cat dev-user.csr | base64 | tr -d '\n'
```

- it show the key

```bash
#LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ2JEQ0NBVlFDQVFBd0p6RVJNQThHQTFVRUF3d0laR1YyTFhWelpYSXhFakFRQmdOVkJBb01DV1JsZGkxbgpjbTkxY0RDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS2t6TjJRWEcxbTJ5eEtxCnF1WDhSWDRrb1JIQkQxNCttcVlBb0REWG1yd1o4SGk4YkFjNkgwRXluVHJ3S243SjNjQTJzS0Y5VVJxUEV1b3IKTlZzUEt1Y09pVE50dzh4bzhvSTBMOGNRRnNGTW94QlBvSTRFMTcyL3JDRjFBSVkxWklrT2hnYkZxRW5BOElMYgowK003blpjalR6bDNlMmZaRk1rQmpQSHpwYTEzTjJIVFZGYzFPRHVvd2FHd2I3clRIOVd6MGcrMlVxdGdWUEpjCktzZFRKNitic256WWo1Y25XSkxZSVIvQ0J4eklrYjAwT1RKNnpYRjYza1JVMFFJc25QU2lnOEd1eVVkYmRlekoKTFU1VStxWE9OQlF5eVpGSWM2ME1nM3A4TE1CTi9KNzZxbmM5S1VzcXMxWWM4ZjErd3MzTWRSUUZlMXh2ZjV6agpLdWdDV2RjQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQkN3VUFBNElCQVFDU05DSmFWQXFCbDVZN3VOU2UwRWw2ClB0MStlUStiVDJSRkFXNDlOWW1GdlRWRDJCaDRwVnlrRXNsSk1tNmlYOGFhdFNBVU1OR2d5b2tBeFp5ZlduWUUKdTdlL1RwWW80ZlVFNUNEUzhNRVdTSzJzYU5oTjMzem1yNmwrVis4MUVKc0VhYzcxbjBLS01sU0xGa0c5WkozdQpEY3NFOE5UaVZ3UUV2VFVQaFREQmZKVzBMQ21XZytlQVNEV1VPbHA0cGJZUzRtTUdMYVo0dGFHUTBkN0JoUmJyCktVa1VzL3NKazJMV2prQWVobHJ6UEk1elhwSS9wanJkUFVPYW42ZWp5K3diZUpSd2c5WVo0S3QwS01Ob0sxU04KdEFzTjNRYkgyb3g0a2pSR0UrTlZmWnhYUlJESFVidU5NdEwyY3Rma3FrUWNTNGxYMzQyd0s4UmFYS1hHeWtzRQotLS0tLUVORCBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0K
```

3. Create a Kubernetes CSR Resource

```bash
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dev-user
spec:
  request: <base64_csr_output_here> # HERE COPY ABOVE CODE
  signerName: kubernetes.io/kube-apiserver-client
  usages:
    - client auth
EOF
```

4. Approve the CSR

```bash
kubectl certificate approve dev-user
```

5. Get the signed cert:

```bash
kubectl get csr dev-user -o jsonpath='{.status.certificate}' | base64 --decode > dev-user.crt
```

- Now you have:

- dev-user.key – Private key
- dev-user.crt – Signed certificate

6. Add User to Kubeconfig

```bash
kubectl config set-credentials dev-user \
  --client-certificate=dev-user.crt \
  --client-key=dev-user.key
```

Then add a context:

```bash
kubectl config set-context dev-user-context \
  --cluster=prathamesh.k8s.local \
  --namespace=development \
  --user=dev-user
```

- to get cluster name

```bash
kubectl config get-clusters
```

7. Create Role & RoleBinding for the User

vi role.yml

```bash
# role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]

---
# binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-user-binding
  namespace: development
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f role.yml
```

8. Test the Access as dev-user

```bash
kubectl --context=dev-user-context get pods
```


- Try to delete:

```bash
kubectl --context=dev-user-context delete pod ib-deployment-649699fd55-4qhhp
# you get forbidden
```

---

- If you want to do all these things in single shot . i have a script in GitHub -- setup-dev-user-rbac.sh

```bash
chmod +x setup-dev-user-rbac.sh
./setup-dev-user-rbac.sh
```

- set the context
```bash
kubectl --context=dev-user-context get pods
```

- try deleting
```bash
kubectl --context=dev-user-context delete pod <pod-name>   
# should be forbidden
```

- Delete the users , role , rolebinding

```bash
kubectl delete rolebinding dev-user-binding -n development
kubectl delete role pod-reader -n development
kubectl delete csr dev-user
kubectl config delete-context dev-user-context
kubectl config delete-user dev-user
rm -f dev-user.key dev-user.crt dev-user.csr
```


---
