## Kubernetes Health Probes (Liveness, Readiness, Startup)

Kubernetes provides health probes to continuously monitor the state of containers running inside Pods.  
These probes help Kubernetes decide:

- When to restart a container
- When to stop sending traffic to a Pod
- When an application has successfully started

Health probes are executed by the kubelet running on each node.

---

### Why Health Probes Are Important

By default, Kubernetes only checks whether a container process is running.  
It does not know if:

- The application is stuck
- The application is temporarily unavailable
- The application has finished starting

Health probes solve this problem by actively checking application health.

---

###  Types of Health Probes

Kubernetes supports three types of probes:

1. Liveness Probe  
2. Readiness Probe  
3. Startup Probe  

Each probe serves a different purpose.

---

## Liveness Probe

### Purpose
The liveness probe determines whether a container is still alive or not.
If the liveness probe fails repeatedly:
  - Kubernetes **restarts the container**


### Key Points
- Controls **container restart**
- Does NOT control traffic routing
- Helps in self-healing applications

---

##  Readiness Probe

### Purpose
The readiness probe determines whether an application is ready to receive traffic or not.
If the readiness probe fails:
  - The Pod is marked as **NotReady**
  - The Service stops routing traffic to the Pod.

The container is **not restarted**

### Key Points
- Controls **traffic flow**

---

## Startup Probe

### Purpose
The startup probe determines whether an application has successfully started or not.

### Why It Exists
Some applications take a long time to start.  
Without a startup probe, liveness checks may fail too early and cause restart loops.
While startup probe is running:
  - Liveness probe is disabled
  - Readiness probe is disabled

If startup probe never succeeds:
  - Container is restarted

### Key Points
- Used for **slow-starting applications**

---

### Internal Working

- Probes are executed by the **kubelet**
- Kubelet sends requests directly to the Pod IP
- Results are evaluated based on configured thresholds
- Actions (restart or traffic removal) are performed automatically

---

## Relationship with Service

- Services route traffic only to **Ready** Pods
- Readiness probe directly affects Service endpoints
- Liveness probe does not affect routing directly


