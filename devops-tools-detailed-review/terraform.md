---
title: "Terraform"
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


# **Terraform**

## **Core Concepts**

* **IaC tool** using declarative syntax (HCL).
* **Idempotent**: same config → same result.
* **Execution plan**: `terraform plan` shows actions before applying.
* **State file** tracks real infrastructure and resource mappings.
* **Providers** are plugins to manage cloud resources (AWS, GCP, Azure, etc.).

---

## **State Management**

* `terraform.tfstate` stores resource attributes.
* **Remote backends** (S3 + DynamoDB, GCS, Azure Blob) enable teamwork + locking.
* **State locking** prevents concurrent writes.
* **State is sensitive** → encrypt + restrict access.
* Use `terraform state rm/mv` for manual state surgery.

---

## **Modules**

* Reusable components of Terraform configuration.
* Improve DRY, structure, and versioning.
* Use **public registry** or private repositories.
* Pin version: `source = "..." version = "x.y.z"`.

---

## **Variables & Outputs**

* Variables defined in `variables.tf`, passed via `.tfvars`, CLI, or environment.
* Sensitive inputs: `sensitive = true`.
* **Outputs** expose resource info (IP, URL) and can be marked sensitive.
* Input variable precedence: CLI > tfvars > environment > defaults.

---

## **Terraform Lifecycle**

* `terraform init` → download providers.
* `terraform validate` → check syntax.
* `terraform plan` → preview changes.
* `terraform apply` → create/update/delete.
* `terraform destroy` → teardown.

---

## **Resource Lifecycle Rules**

* `create_before_destroy` prevents downtime.
* `prevent_destroy = true` protects critical resources.
* `ignore_changes` allows external changes without drift detection.

---

## **Terraform Provisioners**

* **Not recommended** unless no alternative.
* `local-exec` runs local commands.
* `remote-exec` runs commands on remote machines.
* Better use: Ansible, cloud-init, user_data.

---

## **Backends**

* Store & manage state remotely.
* Common backends: **S3 (with DynamoDB lock)**, **Consul**, **Terraform Cloud**, **GCS**, **Azure Blob**.
* Backend configuration cannot use variables.

---

## **Workspaces**

* Logical separation of state within the same config.
* Good for simple multi-env (dev/stage/prod), but many teams prefer **separate state files per environment**.

---

## **Dependency Management**

* Terraform handles dependencies automatically via attributes.
* `depends_on` is used when implicit dependency is missing (use sparingly).

---

## **Drift Detection**

* Run `terraform plan` frequently.
* Config != actual cloud resources → drift.
* Use CI pipelines for automated drift checks.

---

## **Security Best Practices**

* Never store secrets in tfvars or state.
* Use Vault or Cloud KMS/Secrets Manager.
* Restrict who can read `terraform.tfstate`.
* Use S3 bucket encryption & versioning for state.

---

## **Testing & CI/CD**

* **tflint** → syntax + best practices.
* **tfsec / checkov** → security scanning.
* **terratest** → automated infra tests in Go.
* **pre-commit hooks** for fmt, validate.

---

## **Common Interview Questions**

* Explain Terraform state and why it's needed.
* Difference between **local** and **remote** backends.
* What happens if you manually modify resources in AWS?
* Purpose of modules and how to structure them.
* How to handle secrets in Terraform?
* Difference between workspaces vs separate repos/states.
* How to avoid downtime during resource updates?
* Why provisioners are discouraged.

