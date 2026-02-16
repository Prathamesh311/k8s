## Secrets

A Secret in Kubernetes is used to store sensitive information such as:

Database passwords
API keys
SSH keys
TLS certificates
Docker registry credentials

Unlike ConfigMap (non-sensitive data), Secret is meant for confidential data.

WHY: if i dont want to expose the sensitive information so we need to use SECRETS.

By default k8s will create some Secrets these are useful to create communication inside the cluster used to communicate with one resource to another in cluster.
These are system created secrets, we should not delete them.

### TYPES:
Generic: creates secrets from files, dir, literal (direct values).
TLS: Keys and certs.
Docker Registry: used to get private images by using the password.

---

### Command use-case

```bash
kubectl create deploy newdb --image=mariadb
```

```bash
kubectl get pods 
# This will fail because no env variable is set yet.
```

first create secrets , 2 ways to create secrets, from CLI or from File.

```bash
kubectl create secret generic password --from-literal=ROOT_PASSWORD=prath123 
# (from cli) --- literal meaning direct value.
```

```bash
kubectl create secret generic my-secret --from-env-file=vars 
# (from file)
```

```bash
kubectl get secrets
```

```bash
kubectl describe secret my-secret 
# Name of the secret is my-secret.
```

```bash
kubectl set env deploy newdb --from=secrets/my-secret
# setting up env for newdb
```

```bash
kubectl get pods 
# This will fail because we have not mention the MYSQL_ prefix
```


```bash
kubectl set env deploy newdb --from=secret/my-secret --prefix=MYSQL_
# without passing prefix we cant make the pod running status
```

### To see the secrets

```bash
kubectl get secrets my-secret -o yml
# Check Encoded Secret.
```

```bash
echo -n "LKJSKFHJHi" | base64 -d
or
echo -n "LKJSKFHJHi" | base64 --decode
```

```bash
kubectl delete deploy newdb
# delete the deployment newdb
```
