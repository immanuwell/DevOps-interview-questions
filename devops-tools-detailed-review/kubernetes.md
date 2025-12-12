---
title: "Kubernetes"
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
metaLinks:
  alternates:
    - 
---

# **Kubernetes core concepts**

## **Core Concepts**

* Kubernetes is a container orchestration system for managing containerized apps at scale.
* Main objects: **Pod**, **Deployment**, **ReplicaSet**, **Service**, **ConfigMap**, **Secret**, **Ingress**, **PV/PVC**, **Namespace**.
* Control plane components: **API Server**, **Scheduler**, **Controller Manager**, **etcd**.
* Node components: **kubelet**, **kube-proxy**, container runtime (containerd/Docker/CRI-O).

---

## **Pods**

* Smallest deployable unit.
* Usually 1 container per Pod.
* Ephemeral by design — replaced, not repaired.

---

## **Deployments & ReplicaSets**

* Deployment manages ReplicaSets and provides rollout/rollback features.
* Rolling updates by default, supports strategies: **RollingUpdate** and **Recreate**.
* Revision history stored in ReplicaSets.

---

## **Services**

* Stable network endpoint for Pods.
* Types: **ClusterIP**, **NodePort**, **LoadBalancer**, **ExternalName**.
* Uses kube-proxy (iptables or IPVS) for traffic routing.

---

## **Ingress**

* HTTP(S) routing + TLS termination.
* Requires an **Ingress Controller** (NGINX, Traefik, HAProxy, Cilium, etc.).
* Replaces many NodePort/LoadBalancer services.

---

## **ConfigMaps & Secrets**

* ConfigMaps hold non-sensitive config.
* Secrets hold sensitive data (base64-encoded, optionally encrypted at rest).
* Mounted as env vars or files.

---

## **Networking**

* Every Pod gets a unique IP; Pods can communicate without NAT.
* CNI plugins: **Calico**, **Cilium**, **Flannel**, **Weave**.
* kube-proxy manages virtual IPs for services.
* Ingress handles L7; Services handle L4.

---

## **Volumes & Storage**

* **PV** = cluster-level storage resource.
* **PVC** = claim requesting storage.
* **StorageClass** defines dynamic provisioning.
* Common providers: EBS, GCE PD, Ceph, NFS, Longhorn.

---

## **Namespaces**

* Logical isolation in the cluster.
* Important for RBAC, resource quotas, and multi-tenancy.

---

## **RBAC**

* Role/ClusterRole → set of permissions.
* RoleBinding/ClusterRoleBinding → assign permissions.
* Follows the principle of least privilege.

---

## **Autoscaling**

* **HPA**: scales Pods based on CPU/memory/custom metrics.
* **VPA**: suggests or sets resource requests/limits.
* **Cluster Autoscaler**: scales the node pool.

---

## **Probes**

* **Liveness**: restart container if app is stuck.
* **Readiness**: remove from Service endpoints until ready.
* **Startup**: delay other probes until app starts.

---

## **Resource Management**

* `requests` = minimum guaranteed resources.
* `limits` = max allowed resources.
* Poor requests/limits → throttling, OOMKills, bad scheduling.

---

## **Logs & Debugging**

* `kubectl logs`, `kubectl exec`, `kubectl describe`, `kubectl get events`.
* Ephemeral containers for debugging in production.
* Metrics with Prometheus + Grafana.

---

## **Rollouts**

* `kubectl rollout status`, `history`, `undo`.
* Strategies: **rolling**, **blue/green**, **canary** (via Argo Rollouts/Flagger).

---

## **Security**

* Disable anonymous access to API server.
* Limit capabilities via PodSecurityContext.
* Use NetworkPolicies for L3/L4 isolation.
* Store secrets in encrypted storage (Vault, KMS).
* Scan images (Trivy, Anchore, Clair).

---

## **Scheduling**

* Scheduler picks best node based on resources + constraints.
* Affinity/anti-affinity, taints/tolerations influence scheduling.
* NodeSelector = basic placement.
* PriorityClasses control eviction order.

---

## **DaemonSets, Jobs, CronJobs**

* **DaemonSet**: runs 1 Pod per node (logging, monitoring).
* **Job**: run task once until successful.
* **CronJob**: scheduled Jobs.

---

## **etcd**

* Strongly consistent key-value store.
* Stores all cluster state.
* Backups critical for disaster recovery.

---

## **Common Interview Questions to Expect**

* Explain Pod vs Deployment.
* How does service discovery work?
* Difference between Ingress and Service.
* How HPA works under the hood.
* What’s the purpose of RBAC?
* Pod rescheduling logic when a node dies.
* Taints vs tolerations vs affinity.
* Troubleshooting a CrashLoopBackOff.
* How to secure a cluster.
* StorageClass/PV/PVC workflow.


---

# **Kubernetes interview questions**


## **1. What is the difference between a Deployment and a StatefulSet?**

A **Deployment** manages stateless apps with interchangeable pods, while a **StatefulSet** ensures stable network identities, persistent storage per pod, and ordered, deterministic scaling.

---

## **2. How does the Kubernetes scheduler decide where to place a Pod?**

It uses **filtering** (Predicates → node fits requirements) and **scoring** (Priorities → best node) based on CPU/memory requests, taints/tolerations, affinity rules, resource pressure, etc.

---

## **3. What is a Pod disruption budget (PDB)?**

PDB defines the minimum number of pods that must remain available during voluntary disruptions (e.g., node drain), preventing accidental downtime during maintenance.

---

## **4. What's the difference between Requests and Limits?**

**Requests** reserve resources for scheduling; **limits** define the maximum a container can use. Exceeding CPU → throttling; exceeding memory → OOMKilled.

---

## **5. How does Kubernetes handle networking inside a cluster?**

Kubernetes enforces a **flat, routable network** where every Pod gets its own IP; CNI plugins (Cilium, Calico, Flannel, etc.) implement routing, NAT rules, and policies.

---

## **6. What is a DaemonSet and when is it used?**

DaemonSets ensure a copy of a pod runs on **every node** (or matching nodes)—used for logs (FluentBit), monitoring agents (Node Exporter), or CNI components.

---

## **7. What is the difference between a ClusterIP, NodePort, and LoadBalancer service?**

* **ClusterIP**: internal virtual IP
* **NodePort**: exposes on each node’s port
* **LoadBalancer**: provisions external LB from cloud provider and routes to nodes/ClusterIP.

---

## **8. What is the role of kube-proxy?**

It programs iptables/ipvs rules to route service traffic to the correct backend pods. Modern CNIs (Cilium) may replace kube-proxy entirely (kube-proxy replacement).

---

## **9. What are taints and tolerations?**

Taints **repel** pods from nodes; tolerations allow selected pods to be scheduled on those nodes. Used for node segregation (GPU nodes, infra nodes, etc.).

---

## **10. What is an Admission Controller?**

A webhook or built-in module that intercepts API requests—mutating (modify objects) or validating (approve or reject)—used for policies, sidecar injection, security.

---

## **11. What is the use of etcd in Kubernetes?**

etcd stores the entire Kubernetes cluster state—objects, configurations, secrets, node status. It must be backed up regularly and kept highly available.

---

## **12. How does Horizontal Pod Autoscaler (HPA) work?**

HPA adjusts replica count based on metrics like CPU%, memory, or custom metrics via Metrics API, ensuring load-based scaling.

---

## **13. What is the difference between Ingress and Ingress Controller?**

**Ingress** is the routing rule object; **Ingress Controller** is the implementation (NGINX, Traefik, AWS ALB controller) that translates rules into actual load balancer configuration.

---

## **14. Explain Init Containers.**

Init containers run **before** the main container and guarantee startup order—used for migrations, waiting on dependencies, preparing config.

---

## **15. What is a sidecar container?**

A container inside the same pod that extends the main app’s functionality (logging agent, service mesh proxy, file sync, etc.)

---

## **16. How does Kubernetes handle secret encryption at rest?**

Secrets are stored in etcd and can be encrypted using **EncryptionConfiguration** with providers like AES-CBC, AES-GCM, or KMS (AWS KMS, GCP KMS).

---

## **17. What causes a Pod to get stuck in CrashLoopBackOff?**

Repeated container crashes caused by bad configs, missing files, failing health probes, or app-level exceptions; Kubernetes backs off with exponential delay.

---

## **18. What is the difference between livenessProbe, readinessProbe, and startupProbe?**

* **Liveness**: restarts unhealthy containers
* **Readiness**: indicates when pod can receive traffic
* **Startup**: delays other probes until app is fully started

---

## **19. How do NetworkPolicies work?**

They control traffic at the pod level using labels; by default allow-all, but once a policy is applied, traffic becomes deny-all except what's explicitly allowed.

---

## **20. How does Kubernetes perform rolling updates?**

Deployment updates pods gradually using maxSurge/maxUnavailable, ensuring new pods become Ready before old ones terminate—supports automatic rollback on failure.
