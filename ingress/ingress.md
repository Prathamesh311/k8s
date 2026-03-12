## Ingress

- Ingress is a Kubernetes resource that manages external HTTP and HTTPS access to services within the cluster.
- Ingress is a service to expose application, but we already have cluster ip, node port and load balancer.
- Ingress helps to expose HTTP and HTTPS routes from outside of the CLuster
- Ingress supports Host based routing and path based routing
- ingress supports load balancing and SSL termination
- IT redirect the incoming requests to the right services based on the web url or path in the address
- ingress provides encryption feature and helps to balance the load of the applications

### Types of Routing in Ingress

## Host-Based Routing

Host-based routing routes traffic based on the domain name.

- Example
```bash
boom.com
web.boom.com
admin.boom.com
```

Each domain can be directed to different services inside the cluster.


## Path-Based Routing

Path-based routing routes traffic based on the URL path.

- Example:
```bash
boom.com/hello
boom.com/admin
paytm.com/movies
paytm.com/recharge
```

Each path is forwarded to a different service.

### Why Not to Use LoadBalancer or NodePort ?

Services like LoadBalancer, NodePort, and ClusterIP do not support:

- URL-based routing
- Domain-based routing
- Centralized entry point
- SSL termination

A traditional load balancer usually routes traffic based only on ports, not on URL paths.

Ingress solves this problem by acting as a Layer 7 (HTTP) router.

---

### Installing NGINX Ingress Controller

- Before creating an Ingress resource, the NGINX Ingress Controller must be installed in the cluster.

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml
```

```bash
kubectl get pods
kubectl get deploy
kubectl get svc
kubectl get ingress
kubectl get service
```

- refer to nginx.yml

```bash
kubectl apply -f nginx.yml
```

- refer to httpd.yml

```bash
kubectl apply -f httpd.yml
```

- refer to ingress2.yml

```bash
kubectl apply -f ingress2.yml
```

```bash
kubectl get services ingress-nginx-controller --namespace=ingress-nginx
# Wait for 5 mins, it will create a ELB
# Allows All traffic in node security group
```

- Take the ELB and put in the browser http://elb/nginx and http://elb/httpd

---

### Annotations

```bash
nginx.ingress.kubernetes.io/ssl-redirect: "false"
# Disables automatic redirection from HTTP to HTTPS.
```

```bash
nginx.ingress.kubernetes.io/use-regex: "true"
# Enables the use of regular expressions in path matching.
```
 
```bash 
nginx.ingress.kubernetes.io/rewrite-target: /$2
# Rewrites the matched URI to the specified target.​
```

### Rules

- Traffic matching /nginx, optionally followed by a / or end of the string, and any subsequent characters ((.*)), is directed to the nginx service.​
- Traffic matching /httpd, following the same pattern, is directed to the httpd service.​
- All other traffic (/(.*)) is directed to the nginx service.​

Considerations:

### Path Matching:

The pathType: ImplementationSpecific allows the Ingress controller to interpret the path matching rules, including regular expressions. Ensure that your Ingress controller supports this path type and regex patterns.
 ​
### Rewrite Target:

The nginx.ingress.kubernetes.io/rewrite-target: /$2 annotation rewrites the incoming request path to the specified target before forwarding it to the backend service. Ensure that this behavior aligns with your application's routing logic.

---

### Pod State : ImagePullBackOff

- When a kubelet starts creating containers for a Pod using a container runtime, it might be possible the container is in Waiting state because of ImagePullBackOff.

The status ImagePullBackOff means that a container could not start because Kubernetes could not pull a container image for reasons such as

- Invalid image name or
- Pulling from a private registry without imagePullSecret.
- The BackOff part indicates that Kubernetes will keep trying to pull the image, with an increasing back-off delay.

Kubernetes raises the delay between each attempt until it reaches a compiled-in limit, which is 300 seconds (5 minutes).


### Pod State: CrashLoopBackOff

- When you see "CrashLoopBackOff," it means that kubelet is trying to run the container, but it keeps failing and crashing. 
- After crashing, Kubernetes tries to restart the container automatically, but if the container keeps failing repeatedly, you end up in a loop of crashes and restarts, thus the term "CrashLoopBackOff".

---