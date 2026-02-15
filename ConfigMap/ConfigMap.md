# ConfigMaps

It is used to store the data in key-value pair, files, or command-line arguments that can be used by pods, containers in cluster.

But the data should be non-confidential. It does not provide security and encryption.

If you want to provide Enryption use Secrets in K8S.

Limit of the configmap is only 1MB.

But if you want to store more than 1MB configmap data then mount volume or use a separate database or a file service.

---

## Commands use-case

```bash
kubectl create deploy newdb --image=mariadb
# This will create a deployment called newdb with single pod having mariadb
```

```bash
kubectl get pods
```

```bash
kubectl logs newdb-794dd57dbc-tr7s9
# It is crashed because we haven't specified the passsowrd for mariadb.
```

```bash
kubectl set env deploy newdb MYSQL_ROOT_PASSWORD=root123456
# setting env for newdb
```

```bash
kubectl get pods
# Now it will be in running state, but we are passing password directly from command
```

```bash
kubectl delete deploy newdb
```

---

## Passing from Var file.

```bash
kubectl create deploy newdb --image=mariadb
kubectl get pods
kubectl logs newdb-794dd57dbc-f44f2
```
This will not work because haven't specified the password, in above example we have passed password using ENV but lets use now ENVFROM

```bash
# vi vars
MYSQL_ROOT_PASSWORD=root123456
MYSQL_USER=admin
```

lets use configmap and create configmap called dbvars

```bash
kubectl create cm dbvars --from-env-file=vars 
#It will create a configmap called dbvars using var file we created.
```

```bash
kubectl get cm
kubectl describe cm dbvars
```

This will show the plain data.
Now we just created a configmap called dbvars, using this lets deploy our pods

```bash
kubectl set env deploy newdb --from=configmaps/dbvars
```

```bash
kubectl get pods
```

```bash
kubectl get cm
```

```bash
kubectl delete deploy newdb
```