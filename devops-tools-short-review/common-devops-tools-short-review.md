---
title: "Common DevOps tools - short review"
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

# Common DevOps tools - short review

### Observability & Telemetry (metrics, traces, logs, events)

#### OpenTelemetry

OpenTelemetry (often “OTel”) is an open-source, vendor-neutral observability framework that standardizes how you collect and export telemetry data — namely traces, metrics, logs.

OpenTelemetry gives you a unified, open standard for observability, so DevOps can monitor, trace, and diagnose systems more effectively and without vendor lock-in.

![](https://imagedelivery.betterstackcdn.com/xZXo0QFi-1_4Zimer-T0XQ/6e07fd96-5d60-4b3b-bd7b-efae0f38fe00/orig)

#### Prometheus

Prometheus is an open-source systems and service monitoring tool that:

* Collects time-series metrics (data points over time) via HTTP pulls from instrumented targets (services, servers, etc.)
* Stores metrics in a time-series database, with a multidimensional model (metrics + key-value labels)
* Provides PromQL, a powerful query language to aggregate, filter, and analyze metrics
* Supports alerting, by defining rules that trigger when certain conditions are met, and integrates with an Alertmanager to route notifications
* Works well in dynamic, cloud-native environments (e.g. Kubernetes), with service discovery to automatically find new targets

![](https://www.devopsschool.com/blog/wp-content/uploads/2021/01/What-is-Prometheus-Architecutre-components1-740x414.png)

#### Grafana

Grafana is an open-source tool for visualizing, monitoring, and alerting on metrics and time-series data

* It connects to many data sources (Prometheus, InfluxDB, Elasticsearch, etc.) and unifies data into dashboards
* It lets teams create custom, interactive dashboards (graphs, charts, heatmaps, etc.) to monitor system/app health
* It supports alerts — notifying when metrics cross thresholds, so teams can act proactively
* In typical DevOps stacks, it’s used together with metric collectors (e.g., Prometheus) — Prometheus gathers the data, Grafana presents it in a human-friendly way

![](https://grafana.com/media/docs/grafana/dashboards-overview/complex-dashboard-example.png)

#### Loki

Loki is a log aggregation system by Grafana Labs

* It’s horizontally scalable, multi-tenant, and designed to be cost-efficient
* Unlike many logging systems, it does not index full log contents. Instead, it only indexes metadata/labels per log stream (e.g. “service=web”, “instance=xyz”)
* The actual log lines are stored in compressed chunks (often on object storage)
* You query logs using LogQL, a query language similar in spirit to PromQL (for metrics)
* It integrates well with Grafana (for dashboards & viewing logs) and with Kubernetes / Prometheus-based setups

![](https://grafana.com/docs/loki/latest/get-started/loki-overview-2.png)

#### Datadog

Datadog is a cloud-based observability and monitoring platform used in DevOps to collect, correlate, and visualize metrics, logs, and traces from infrastructure and applications across an entire stack

* get unified visibility across servers, containers, databases, networks, and services
* detect anomalies and performance issues via real-time alerts
* correlate data (metrics, logs, traces) to speed up root cause analysis
* track “DORA” DevOps metrics (deployment frequency, lead time, etc.)

![](https://d1.awsstatic.com/Solution%20Space%20\(CRS\)/DataDog-solution-diagram.7cf704534940c0431f3ce7b025b577084312c19e.png)

### Infrastructure as Code (IaC) & Automation

#### Terraform

Terraform (by HashiCorp) is an Infrastructure as Code (IaC) tool used in DevOps to declare and automate the provisioning, updating, and teardown of infrastructure (cloud, on-premises, services) via simple configuration files

* You write declarative configs (describing what you want, not how to do it)
* Terraform figures out the execution plan, dependencies, and the steps to realize the desired state
* It maintains a state file to track what’s actually deployed vs. what’s declared
* Supports multiple providers (AWS, Azure, GCP, etc.), so you can manage hybrid or multi-cloud infrastructure

![](https://www.devopsschool.com/blog/wp-content/uploads/2022/02/terraform-1.png)

#### Pulumi

Pulumi is an Infrastructure as Code (IaC) tool/platform that lets DevOps teams define, deploy, and manage cloud infrastructure using general-purpose programming languages (e.g. Python, TypeScript, Go, C#) instead of domain-specific configuration languages

This enables:

* richer logic (loops, conditionals, abstractions) in infrastructure code
* reuse and modularization, with same tooling as application code
* integration into CI/CD pipelines for automated provisioning and updates
* consistent, versioned, testable infrastructure definitions

In DevOps, Pulumi helps bridge development and operations by treating infrastructure like software, improving automation, reproducibility, and maintainability

![](https://miro.medium.com/v2/resize:fit:778/1*5izfpIf-ITcNMhvn0fgEOA.png)

#### Ansible

Ansible is an open-source automation tool used in DevOps to manage infrastructure, deploy applications, and orchestrate workflows

Key traits:

* Agentless: It doesn’t require software agents on the target machines; it uses SSH (or WinRM on Windows) to run tasks remotely
* Declarative / Idempotent: You describe the desired state of the system in Ansible playbooks (YAML). Running the same playbook multiple times will not change the system further once it’s in the intended state
* Playbooks & Modules: Work is organized into playbooks (YAML files) composed of tasks that call modules (prebuilt actions)

![](https://miro.medium.com/v2/1*RNqdUIDLAcKlwz9bWhXYjA.png)

#### CloudFormation

AWS CloudFormation is an Infrastructure as Code (IaC) service that lets you define, provision, and manage AWS resources via templates written in JSON or YAML

In a DevOps context:

* It treats infrastructure like software — versioned, reviewed, and automated.
* You bundle related resources into a _stack_, and can update or delete them as a single unit
* Before applying changes, you can generate a _change set_ to preview impacts
* It enforces consistency, repeatability, and automation in deployment pipelines (reduces manual error)

![](https://cdn.prod.website-files.com/5ff66329429d880392f6cba2/663a67f3cc5a74576d877d5d_10%20-%207.05-min.jpg)

### Continuous Integration / Continuous Delivery (CI/CD) / GitOps

#### Jenkins

Jenkins is an open-source automation server used in DevOps for implementing CI/CD (Continuous Integration / Continuous Delivery) pipelines

In practice, Jenkins:

* Automates building, testing, and deploying code changes
* Lets you define pipelines as code (via a “Jenkinsfile”) so the workflow is versioned with your source
* Works with agents/workers to distribute tasks across machines
* Supports extensibility through a rich plugin ecosystem, integrating with tools like Git, Docker, Kubernetes, build tools, etc

#### GitHub Actions

GitHub Actions is an automation tool built into GitHub that lets you define workflows (via YAML files) to run jobs (build, test, deploy, etc.) in response to events like code pushes, pull requests, schedules, etc

In DevOps, GitHub Actions is used to implement CI/CD (Continuous Integration / Continuous Deployment) pipelines, so that code changes are automatically tested and deployed — reducing manual work, detecting issues early, and speeding up delivery

#### GitLab CI

GitLab CI is the built-in continuous integration and delivery tool inside GitLab — it automates building, testing, and deploying code changes whenever you push commits

Key points:

* Pipelines are defined in a `.gitlab-ci.yml` file stored in your repo
* A pipeline consists of stages (e.g. build → test → deploy), each containing jobs
* Runners execute jobs (they can be GitLab-hosted or self-managed)
* It is central to DevOps because it enables faster feedback loops, fewer manual errors, and more frequent, reliable releases

#### Argo CD

Argo CD is an open-source continuous delivery tool made for Kubernetes

* It follows the GitOps model: your Git repository holds the “desired state” of applications (manifests, configs), and Argo CD continuously pulls from Git and applies changes to the cluster to reconcile any drift
* Argo CD monitors the cluster, detects when the actual state diverges from the desired, and can automatically sync them (or alert/manual sync) to keep them in alignment
* It supports declarative application definitions via YAML, Helm, Kustomize, Jsonnet, etc
* You get a visual UI, CLI, role-based access control (RBAC), and auditability (everything is versioned via Git) as part of the solution

![](https://redhat-scholars.github.io/argocd-tutorial/argocd-tutorial/_images/argocd-sync-flow.png)

#### FluxCD

FluxCD is a GitOps-based Continuous Delivery tool for Kubernetes

* You declare the desired state of your apps and infrastructure in Git
* FluxCD runs controllers inside the cluster that continuously reconcile the real cluster state to match what’s in Git
* It supports Helm, Kustomize, image automation, and integrates with common tools
* Changes are auditable (since Git is your source of truth), and rollbacks are easier

![](https://miro.medium.com/v2/1*w-bsObUznAP7arn39lNY7g.png)

### Containers & Container Orchestration & related tools

#### Docker / container runtimes

Docker is an open-source containerization platform that packages an application and all its dependencies (libraries, runtime, config) into a container. This container can run reliably on any environment—developer laptop, test server, or production—because it encapsulates everything the app needs.

In DevOps, Docker plays a key role as an enabler by:

* Ensuring consistency across environments (no “it works on my machine” surprises)
* Accelerating CI/CD pipelines (build, test, deploy) via automated container tasks
* Improving resource efficiency (containers are lighter than full VMs)

![](https://www.znetlive.com/blog/wp-content/uploads/2018/01/dev3.png)

#### Kubernetes

Kubernetes (often “K8s”) is an open-source container orchestration platform that automates deployment, scaling, and management of containerized applications

In a DevOps context, Kubernetes helps by:

* Allowing teams to deploy complex microservices reliably and reproducibly
* Automatically scaling applications up or down based on demand
* Providing self-healing (restarting failed containers, replacing them)
* Enabling declarative “desired state” configuration and rolling upgrades with minimal downtime

#### Helm

Helm is a package manager for Kubernetes used in DevOps to simplify deploying, upgrading, and managing applications

Key ideas:

* A Helm chart bundles all Kubernetes resource templates (deployment, service, ingress, etc.) + default configuration values into a reusable package
* You install a chart into a cluster as a release — Helm tracks versions, supports upgrades, rollbacks, and configuration overrides
* Helm charts help avoid duplicating YAMLs and make configuration across environments (dev, staging, prod) more consistent and manageable

![](https://www.howtouselinux.com/wp-content/uploads/2024/03/k8s-helm.webp)

### Security / Secrets / Compliance Integration (DevSecOps)

#### HashiCorp Vault

HashiCorp Vault is a secrets management and identity-based security tool used in DevOps to centrally store, manage, and control access to sensitive credentials (e.g. passwords, API keys, certificates, encryption keys)

Key features & benefits:

* Secure storage & encryption — Data is encrypted at rest and in transit
* Dynamic secrets — Vault can generate temporary credentials (e.g. database users) on-demand and revoke them, reducing reliance on long-lived static secrets
* Access control & policies — Uses identity-based authentication (tokens, AppRole, LDAP, etc.) and fine-grained policies to restrict who/what can access which secrets
* Audit & logging — Tracks all access and changes to secrets for compliance and security monitoring
* “Encryption as a service” — Vault can act as a crypto backend, performing encryption/decryption or signing operations without exposing keys to clients

In a DevOps pipeline, Vault helps avoid insecure practices like embedding credentials in code or config files, and enables more secure, automated secret handling across environments (development, staging, production).

![](https://www.hashicorp.com/_next/image?url=https%3A%2F%2Fwww.datocms-assets.com%2F2885%2F1642009541-vault-auth-basic.png\&w=1920\&q=75)

#### AWS Secrets Manager

AWS Secrets Manager is a managed service by AWS that lets you securely store, manage, and access “secrets” — such as database credentials, API keys, tokens, etc.

Secrets Manager is used to:

* Remove hard-coded secrets from code and config files
* Dynamically fetch secrets at runtime (e.g. via SDK or CLI)
* Automatically rotate secrets on a schedule (often via AWS Lambda)
* Enforce fine-grained access control via IAM policies
* Audit secret usage through AWS CloudTrail and logging

So, in DevOps pipelines, instead of embedding passwords or keys, your build/deploy jobs or applications call Secrets Manager to retrieve the secrets they need. This improves security, reduces exposure, and centralizes secret management.

![](https://www.zippyops.com/userfiles/cache/thumbnails/1920/tn-spring-boot-aws-secrets-manager-122569815.png)

#### policy-as-code (e.g. OPA / Gatekeeper)

Open Policy Agent (OPA) is an open-source, general-purpose policy engine that enables “policy as code” — letting you write, version, test, and enforce policies automatically rather than manually.

In a DevOps / DevSecOps context, OPA is used to embed policy checks into your pipelines and infrastructure, such as:

* Validating Terraform plans before applying them
* Enforcing Kubernetes admission policies (e.g. “no containers running as root”)
* Authorizing API calls or microservice requests based on rules
* Ensuring configuration files (Dockerfiles, YAML, etc.) comply with standards

OPA uses a declarative language called Rego to express policies, and it evaluates input data (in JSON form) against those policies to return decisions (allow/deny or structured responses)

Because policies are decoupled from application logic, OPA enables a consistent, scalable, and auditable way to enforce governance across your stack.

![](https://miro.medium.com/v2/resize:fit:1400/1*Z8A9yl_MMf7j4Q-iq3SbBg.png)

### SAST/DAST scanning tools

#### SonarQube

SonarQube is a static code analysis tool used in DevOps pipelines to continuously inspect code quality, detect bugs, vulnerabilities, code smells, and technical debt.

It integrates into CI/CD workflows (e.g. with Jenkins, Azure DevOps, GitLab) to enforce quality gates — i.e. rules that must be passed before code can be merged or deployed.

In short: SonarQube automates code quality checks so bad code doesn’t slip into production.

#### OWASP ZAP

OWASP ZAP (Zed Attack Proxy) is an open-source dynamic application security testing (DAST) tool that you can embed into DevOps pipelines.

In a DevOps / DevSecOps context, ZAP is used to:

* Automatically scan running applications (or APIs) during build/test phases for vulnerabilities like SQL injection, XSS, insecure authentication, etc.
* “Shift left” security by giving feedback early in the pipeline (before deployment)
* Run in headless or containerized modes (e.g. via Docker) with APIs or scripts, enabling integration with CI/CD tools like Jenkins, Azure DevOps, GitLab, etc.
* Produce reports (HTML, XML, etc.) and gates (fail the build if certain severity thresholds are met)

In short: OWASP ZAP helps automate security testing in DevOps pipelines by scanning live apps, catching vulnerabilities early, and enforcing security gates before production.

![](https://www.infosectrain.com/wp-content/uploads/2025/01/Key-Features-of-OWASP-ZAP.png)

#### supply-chain security (Sigstore, in-toto)

Sigstore is an open-source framework designed to improve software supply chain security by making it easy to sign, verify, and audit software artifacts (binaries, containers, etc.)

Key ideas:

* It uses short-lived, identity-bound certificates instead of requiring developers to manage long-term signing keys
* It logs all signing events (and metadata) in an immutable transparency log (Rekor) so anyone can audit who signed what, when
* Its components include Cosign (for signing/verification), Fulcio (certificate authority), and Rekor (logging)
* In DevOps pipelines, it enables automated signing & verification of build artifacts, enforcing integrity and traceability with minimal manual key management burden.

### eBPF / Kernel-level Observability, Network Introspection

#### Cilium

Cilium is an open-source networking, security, and observability layer for containerized/cloud-native environments (especially Kubernetes)

Key points:

* It uses eBPF (extended Berkeley Packet Filter) inside the Linux kernel to run networking and security logic very efficiently
* It can replace or augment traditional CNI (Container Network Interface) plugins and even replace kube-proxy (which handles service traffic) using kernel-level load balancing
* It supports fine-grained network policies (L3 → L7), identity-based security, encryption, and observability (via its companion “Hubble”) without changing application code

In short: Cilium brings high performance, secure, and observable networking to modern container platforms.

![](https://miro.medium.com/v2/resize:fit:1400/1*xgKipZWmzAmtWKYhHdk9mw.png)
