---
title: "Docker and container runtimes"
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

# **Docker and container runtimes**

## **Core Concepts**

* Docker packages applications into **containers**: isolated, reproducible environments.
* Containers share the **host OS kernel**, unlike VMs. Lightweight and fast.
* Containers run from **images** (immutable templates).
* A Dockerfile defines **how an image is built**.
* Layers are **cached**, enabling fast rebuilds.
* A container runtime (Docker, containerd, CRI-O) executes containers.

***

## **Docker Architecture**

* **Docker Engine** = Docker CLI + Docker API + Docker daemon + container runtime.
* Docker now uses **containerd** under the hood.
* **runc** is the low-level OCI-compliant runtime that actually spawns containers.
* Separation of concerns:
  * Docker CLI → user interface
  * Dockerd → builds & manages images/containers
  * containerd → container lifecycle
  * runc → executes containers according to OCI spec

***

## **Images & Layers**

* Docker images are **layered filesystems** (UnionFS).
* Each Dockerfile line creates a new **layer**.
* Layers are **read-only**; containers add a writable layer on top.
* Layers are **reused across images**, reducing disk usage.
* `COPY` and `RUN` create layers; `CMD` and `ENTRYPOINT` do not.

***

## **Container Lifecycle**

* Key states: **created → running → paused → stopped → removed**.
* `docker run` = create + start.
* Containers should be **stateless**; data must go to volumes.

***

## **Volumes & Storage**

* Volumes persist data outside container lifecycle.
* Types: **volumes**, **bind mounts**, **tmpfs**.
* Volumes recommended for production (managed by Docker).
* Bind mounts recommended for local development.

***

## **Networking**

* Default driver: **bridge**.
* Others: **host**, **none**, **overlay**, **macvlan**.
* Overlay networks commonly used in **Docker Swarm** and Kubernetes (via CNI).
* `docker run -p` maps container port → host port.

***

## **Dockerfile Best Practices**

* Use small base images (`alpine`, `distroless`, `scratch`).
* Use **multi-stage builds** to reduce size.
* Use `.dockerignore` to avoid huge contexts.
* Avoid `ADD` unless necessary; prefer `COPY`.
* Always define `USER` (drop root).
* Keep layers minimal and logical.

***

## **ENTRYPOINT vs CMD**

* **ENTRYPOINT**: fixed executable.
* **CMD**: default arguments.
* Combine both:

```dockerfile
ENTRYPOINT ["myapp"]
CMD ["--help"]
```

***

## **Security**

* Never run containers as **root**.
* Use **read-only root filesystem** when possible.
* Scan images with `docker scan` or Trivy.
* Use minimal images to reduce attack surface.
* Namespaces, cgroups, AppArmor/SELinux provide isolation.
* OCI runtimes enforce seccomp and capabilities.

***

## **containerd (Important for interviews)**

* containerd is a **container runtime** used by Docker and Kubernetes.
* Kubernetes uses containerd via **CRI** (Container Runtime Interface).
* Features:
  * Image management
  * Snapshotters (overlay2, btrfs, zfs)
  * Container lifecycle
  * gRPC API
* runc used by containerd for OCI container execution.
* Lightweight, fast, production-grade runtime.

***

## **OCI (Open Container Initiative)**

* OCI defines two specs:
  * **Image spec** (how images are structured)
  * **Runtime spec** (how containers are executed)
* Ensures compatibility between Docker, containerd, CRI-O, Podman.

***

## **Docker Compose**

* Compose manages multi-container applications.
* `docker-compose.yml` defines services, networks, and volumes.
* Used for development; not recommended for production.
* Can use profiles to enable/disable specific services.

***

## **Performance**

* Containers start in **milliseconds**.
* Low overhead since they share host kernel.
* Use `docker stats` for monitoring.
* Use build cache + multi-stage builds for optimal performance.

***

## **Common Interview Q\&A Insights**

* **Q: Docker vs containerd?** A: Docker is a full platform (CLI + daemon + build system); containerd is the low-level runtime Docker uses. Kubernetes uses containerd directly.
* **Q: How do containers isolate processes?** A: Linux **namespaces**, **cgroups**, **capabilities**, **seccomp**, and **rootfs**.
* **Q: Why are images immutable?** A: To ensure reproducibility, predictability, and fast deployment.
* **Q: How do you reduce image size?** A: Multi-stage builds, smaller base images, fewer layers, proper caching.
* **Q: Difference between `ENTRYPOINT` and `CMD`?** A: ENTRYPOINT = main command; CMD = default args.
* **Q: Persistent data strategy?** A: Volumes or external storage; containers should stay stateless.

---

## **Production-grade Dockerfile example**

```dockerfile
FROM golang:1.23-alpine AS builder

LABEL org.opencontainers.image.title="my-go-service" \

# Build-time variables
ARG VERSION="dev"

# Runtime env in builder (affects build tools)
ENV CGO_ENABLED=0 \
    GO111MODULE=on \
    GOOS=$TARGETOS \
    GOARCH=$TARGETARCH

# Set shell (handy for strict mode)
SHELL ["/bin/sh", "-ceu"]

WORKDIR /src

# Install build deps (if you need git, ca-certs, etc.)
RUN apk add --no-cache ca-certificates tzdata git

# Copy only go.mod/go.sum first to maximize cache reuse
COPY go.mod go.sum ./
RUN --mount=type=cache,target=/go/pkg/mod \
    go mod download

COPY . .

# Build (with cached build dir)
RUN --mount=type=cache,target=/root/.cache/go-build \
    go build -trimpath \
    -ldflags "-s -w \
      -X main.version=${VERSION} \
      -X main.commit=${COMMIT_SHA}" \
    -o /out/app ./cmd/app

# Minimal runtime stage
FROM gcr.io/distroless/static-debian12:nonroot AS runtime

# Runtime env
ENV APP_ENV=production \
    TZ=UTC \
    PORT=8080

WORKDIR /app

# Copy binary from builder
COPY --from=builder /out/app /app/app

USER nonroot:nonroot

EXPOSE 8080

# Optional volume (for mutable data, logs, etc.)
VOLUME ["/app/data"]

# Healthcheck (note: distroless has no shell; use exec-form)
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD ["/app/app", "healthcheck"]

# Signal for graceful shutdown
STOPSIGNAL SIGTERM

# Entry + default args
ENTRYPOINT ["/app/app"]
CMD ["serve"]
```


---

# **Docker interview questions**


## **1. What is the difference between an image and a container?**

**Answer:**
An **image** is a read-only blueprint (layers + metadata).
A **container** is a **running instance** of that image with a writable layer on top.

---

## **2. What is a Docker layer and why is it important?**

**Answer:**
Layers are incremental filesystem snapshots in an image.
They enable:

* **Caching** (build speed)
* **Deduplication** (less storage)
* **Fast distribution** (shared base layers)

---

## **3. What is the purpose of a multi-stage build?**

**Answer:**
Multi-stage builds allow using multiple `FROM` statements to:

* Separate build environment from runtime
* **Reduce image size drastically**
* Improve security by shipping only binaries/artifacts

---

## **4. What is the difference between CMD and ENTRYPOINT?**

**Answer:**

* **CMD**: default arguments (can be overridden).
* **ENTRYPOINT**: main executable (hard to override).
  Best practice: `ENTRYPOINT` + `CMD` for flexible arguments.

---

## **5. How does Docker isolate processes?**

**Answer:**
Using Linux kernel features:

* **Namespaces** → isolation (PID, NET, IPC, MNT, UTS, USER)
* **Cgroups** → resource limits
* **Seccomp/AppArmor** → syscall filtering

---

## **6. What is the difference between Docker volumes and bind mounts?**

**Answer:**

* **Volumes**: managed by Docker, portable, safer, preferred for production
* **Bind mounts**: direct host paths, useful for development

---

## **7. How can you reduce Docker image size?**

**Answer:**

* Use **multi-stage builds**
* Use smaller base images (`alpine`, `distroless`)
* Combine RUN steps
* Clean cache (`apt-get clean`, etc.)
* Avoid unnecessary files (`.dockerignore`)

---

## **8. How does Docker networking work (bridge mode)?**

**Answer:**
Default is the **bridge network**, where:

* Each container gets a private IP
* Docker sets up NAT using iptables
* Containers communicate via virtual Ethernet pairs

---

## **9. What are the different Docker network types?**

**Answer:**

* **bridge** – default, NAT
* **host** – no isolation, shares host network
* **none** – no networking
* **overlay** – multi-host for Swarm
* **macvlan** – containers get real MAC/IP

---

## **10. What is the purpose of `.dockerignore`?**

**Answer:**
Excludes files from the build context →

* Faster builds
* Smaller images
* Avoids leaking secrets

---

## **11. How does Docker handle container logs?**

**Answer:**
Docker logs to **stdout/stderr** using a logging driver (default: `json-file`).
Other drivers: `syslog`, `journald`, `fluentd`, Loki, etc.

---

## **12. What happens when you run `docker run`?**

**Answer:**
Docker:

1. Pulls the image (if missing)
2. Creates container writable layer
3. Sets up networking
4. Applies cgroups/namespace settings
5. Starts ENTRYPOINT/CMD

---

## **13. What is an ephemeral container?**

**Answer:**
A container that:

* Has no persistent data
* Can be destroyed and recreated easily
  Used for stateless microservices.

---

## **14. What is Docker BuildKit and why is it better?**

**Answer:**
BuildKit improves builds by:

* Parallel execution
* Layer-level caching
* Secret mounts
* Better dependency detection
* Faster and safer builds

---

## **15. How do you copy secrets into a Docker build securely?**

**Answer:**
Use BuildKit secret mounts:

```bash
RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret
```

Secrets **are not stored** in the final image.

---

## **16. How do you limit resources for a Docker container?**

**Answer:**
Using cgroups options:

```bash
docker run --memory=512m --cpus=1
```

---

## **17. What’s the difference between Docker Compose and Docker Swarm?**

**Answer:**

* **Compose**: local multi-container apps
* **Swarm**: clustering + scheduling + HA
  Compose is for development; Swarm is for orchestration.

---

## **18. How do you run a container in read-only mode?**

**Answer:**

```bash
docker run --read-only --tmpfs /tmp image
```

Increases security and prevents file modifications.

---

## **19. What is the difference between `docker stop` and `docker kill`?**

**Answer:**

* **stop** → sends SIGTERM, graceful shutdown
* **kill** → sends SIGKILL, immediate termination

---

## **20. How do you debug a running container?**

**Answer:**

* `docker exec -it <id> sh/bash`
* Use `docker logs`
* Use `docker inspect` for metadata
* Use `nsenter` for deep namespace debugging
