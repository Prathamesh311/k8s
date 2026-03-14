## sidecar

- A Sidecar container is a secondary container that runs alongside the main application container within the same Pod.
- A Sidecar creates a helper container for the main container.
- The main container runs the application and the helper container assists the main container.

### Adapter Design Pattern

- The Adapter pattern is used to standardize the output pattern of the main container.

### Ambassador Design Pattern

- The Ambassador pattern is used to connect containers with the outside world.

### Init Container

- An Init Container initializes the first work and exits later.

This design pattern allows extending or enhancing the functionality of the main application without modifying its code.


#### Common use cases include:

- Log aggregation
- Data synchronization
- Proxying network traffic

---

### Example: Log Aggregation with a Sidecar Container

- In this example, the main application container writes log data to a shared volume, and the sidecar container serves these logs over HTTP using Nginx.

```bash
refer to sidecar.yml
```

### Main Application Container (app-container):

- Uses the alpine image, a minimal Docker image based on Alpine Linux.​
- Executes a shell command that appends the current date and time to /var/log/app.log every five seconds.​
- Mounts a shared volume at /var/log to store log data.​

### Sidecar Container (sidecar-container):

- Uses the nginx image to serve content over HTTP.​
- Mounts the same shared volume at /usr/share/nginx/html, which is the default directory Nginx serves files from.​
- As a result, Nginx serves the app.log file, allowing access to the application's log data via HTTP.​

### Shared Volume (shared-logs):

- An emptyDir volume that is created when the Pod is assigned to a node and exists as long as the Pod is running.​
- Provides a shared storage space accessible to both containers for log data.​
- emptyDir a temporary directory that exists as long as the Pod runs.

```bash
kubectl apply -f sidecar.yaml
```

```bash
kubectl get pods
```

```bash
kubectl describe pods
```

```bash
kubectl exec -it log-aggregator-pod -c sidecar-container -- sh
# -c used when we have two or more containers in a pod, to access the particular container 
```

```bash
cd /usr/share/nginx/html
cat app.log
```

- we can see app.logs which is generated in app-container but can be accessed from side-car container using shared volumes.

```bash
kubectl delete -f sidecar.yml
```

---