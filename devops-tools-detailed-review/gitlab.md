---
title: "GitLab CI/CD"
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


# **GitLab CI/CD core concepts**

## **Core Concepts**

* GitLab CI/CD runs pipelines defined in **.gitlab-ci.yml**.
* Pipelines consist of **stages** (build → test → deploy).
* Each stage contains **jobs**, executed by **GitLab Runners**.
* Runners can be **shared**, **group**, or **project** level.
* Pipelines run automatically on **push**, **MR**, or **manual triggers**.

---

## **.gitlab-ci.yml Basics**

* Must be in repository root.
* Default structure:

  ```yaml
  stages: [build, test, deploy]
  job_name:
    stage: build
    script:
      - command
  ```
* Use **only/except** or **rules:** to control job execution.
* Use **artifacts** to pass files to the next stage.

---

## **Runners**

* Runner types: **shell**, **Docker**, **Kubernetes**, **custom** executors.
* **Docker executor** is most common; provides clean isolated container.
* Runners need **registration token** to connect to GitLab.
* Tags allow selecting which runner handles which jobs.

---

## **Caching & Artifacts**

* **Cache** speeds up repeated builds (dependencies, modules).
* **Artifacts** persist data between stages (binaries, test reports).
* Artifacts expire automatically unless defined otherwise.

---

## **Variables**

* Defined in GitLab UI or `.gitlab-ci.yml`.
* Can be **protected**, **masked**, **file type**.
* Use `$CI_COMMIT_SHA`, `$CI_PIPELINE_ID`, `$CI_JOB_STAGE`, etc.

---

## **Environments & Deployments**

* GitLab tracks **environments** (dev, staging, prod).
* **Auto DevOps** can deploy automatically via Kubernetes.
* **Manual jobs** used for approval workflows (e.g., production releases).
* **Protected environments** restrict who can deploy.

---

## **Pipeline Types**

* **Basic pipeline**: sequential stages.
* **Parent/Child pipelines**: modularize large CI configs.
* **Multi-project pipelines**: chain pipelines across repositories.
* **Dynamic pipelines**: jobs created via templates and rules.

---

## **Rules vs Only/Except**

* `rules:` is more powerful.
* Prefer `rules:` for conditional logic, branch logic, MR logic.
* Example:

  ```yaml
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: always
  ```

---

## **Security & Compliance**

* **Secret variables** stored securely in GitLab.
* **Protected variables** only for protected branches/tags.
* GitLab provides SAST, DAST, dependency scanning templates.
* Use **CI/CD security scanning** for compliance pipelines.

---

## **Artifacts & Reports**

* GitLab supports:

  * **JUnit test reports**
  * **Code coverage**
  * **Performance reports**
  * **Terraform/Helm reports**
* Shown in MR automatically.

---

## **Triggers**

* Trigger pipeline via:

  * **API trigger tokens**
  * **Schedules**
  * **Webhooks**
  * **Multi-project triggers**

---

## **Best Practices**

* Keep pipeline **fast** (parallel jobs, caching).
* Use **Docker-in-Docker** only when necessary.
* Prefer **kaniko** or **buildah** in Kubernetes runners.
* Split pipelines using **child pipelines** for maintainability.
* Use **templates** (`include:`) for reusable CI logic.
* Make pipelines **idempotent** and **hermetic**.

---

## **Common Interview Questions**

* How does GitLab Runner work?
* Difference between **cache** and **artifacts**?
* What are **rules**? How do you control when jobs run?
* Describe deployment to protected environments.
* How do you speed up pipelines?
* How to store and protect secret variables?
* Explain parent/child pipelines.
* Difference between **tags** and **environments**?
* How do you run CI across multiple repositories?
* How do you build Docker images securely in CI?

---

# **GitLab CI/CD interview questions**


## **1. What is a GitLab Runner?**

A lightweight agent that executes CI jobs. It pulls the job’s configuration from `.gitlab-ci.yml`, runs scripts, and reports results back to GitLab.

---

## **2. What is the purpose of `.gitlab-ci.yml`?**

Defines the entire pipeline: stages, jobs, rules, artifacts, caching, environment deployments, variables, and conditions.

---

## **3. What are GitLab CI/CD stages?**

Ordered groups of jobs (`build`, `test`, `deploy`). Jobs in the same stage run in parallel; stages run sequentially.

---

## **4. What is the difference between `only`/`except` and `rules`?**

* `only`/`except`: simple, legacy filtering.
* `rules`: more powerful, supports conditional logic, variables, file changes, pipeline source, etc.

---

## **5. What are GitLab CI artifacts?**

Files saved after a job runs (e.g., build outputs, test reports). Later jobs can download them.

---

## **6. What is GitLab CI cache used for?**

Stores dependencies to speed up pipelines (e.g., npm modules, Go modules). Not passed between jobs by default, unlike artifacts.

---

## **7. How does GitLab handle environment deployments?**

Using the `environment:` keyword. Allows tracking deployments, rollbacks, and environment history in GitLab UI.

---

## **8. What is a manual job?**

A job that requires human approval. Set using `when: manual`.

---

## **9. What is a scheduled pipeline?**

A pipeline automatically triggered at specific intervals via GitLab schedule settings.

---

## **10. What is the difference between `needs:` and stages?**

`needs:` allows **DAG**-based pipelines by running jobs out of stage order as soon as dependencies finish.

---

## **11. What are protected branches in GitLab?**

Branches that restrict:

* who can push
* who can merge
* who can run deployments

Often used for production.

---

## **12. What are GitLab predefined environment variables?**

Automatically injected CI variables (e.g., `CI_COMMIT_SHA`, `CI_JOB_ID`, `CI_PIPELINE_SOURCE`, `CI_PROJECT_PATH`).

---

## **13. What is a child pipeline?**

A pipeline triggered from another pipeline for modular, reusable CI configurations.

---

## **14. What are GitLab’s pipeline types?**

* Push pipelines
* Merge request pipelines
* Schedules
* Web/API triggered
* Child/parent pipelines

---

## **15. What is a pipeline artifact report?**

Special artifact types (JUnit, coverage reports, SAST results) that GitLab parses and shows visually in UI.

---

## **16. How do you optimize GitLab pipelines for performance?**

* Use `needs:` to create DAG pipelines
* Cache dependencies
* Use parallel jobs (`parallel:`)
* Use `rules` to skip unnecessary runs
* Prebuild Docker images
* Use remote runners with autoscaling

---

## **17. How would you deploy to multiple environments (dev, staging, prod)?**

Use multiple jobs with different environments:

```yaml
environment:
  name: production
  url: https://app.com
```

Combine with:

* `rules`
* `when: manual` for production
* Protected environments

---

## **18. How do you secure secrets in GitLab CI?**

* Use GitLab **CI/CD variables** (masked, protected)
* Use HashiCorp Vault integration
* Avoid storing secrets in `.gitlab-ci.yml`
* Use environment protection

---

## **19. What is GitLab’s Vault integration?**

Built-in integration allowing jobs to dynamically retrieve secrets from Vault using JWT authentication.

---

## **20. What are runner executors in GitLab?**

Environments used by runners:

* Shell
* Docker
* Docker Machine
* Kubernetes
* VirtualBox
* SSH executor

---

## **21. How do you run GitLab CI pipelines in Kubernetes?**

Use the **Kubernetes executor**: jobs run as pods. Supports autoscaling and ephemeral environments.

---

## **22. How do you use include templates in GitLab CI?**

Reuse configs from other projects or GitLab templates:

```yaml
include:
  - template: Python.gitlab-ci.yml
```

---

## **23. What is the difference between artifacts and dependencies?**

* `artifacts`: store job output
* `dependencies`: specify which job to download artifacts from

---

## **24. What is a multi-project pipeline?**

Pipelines triggered across multiple repos—useful for monorepos or interdependent services:

```yaml
trigger:
  project: group/repo
```

---

## **25. How do you use `rules: changes`?**

Run jobs only if certain files change:

```yaml
rules:
  - changes:
      - src/**
```

---

## **26. How can you prevent pipeline execution on push but run only on merge requests?**

```yaml
rules:
  - if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

---

## **27. How do you generate dynamic environments for feature branches?**

Use `environment:name` with variable expansion:

```yaml
environment:
  name: review/$CI_COMMIT_REF_SLUG
  url: https://review.example.com/$CI_COMMIT_REF_SLUG
```

---

## **28. How do you add CI/CD pipeline test coverage visualization?**

Use coverage regex:

```yaml
coverage: '/^TOTAL.*\s(\d+\%)$/'
```

Displayed in UI under pipeline results.

---

## **29. How do you restrict who can deploy to production?**

Use **protected environments** and **protected variables**, allowing only certain roles or groups to run deployment jobs.

---

## **30. How do you handle monorepo pipelines in GitLab?**

Key strategies:

* `rules: changes` to run only affected components
* Separate `.gitlab-ci.yml` files per directory
* Use child pipelines for modular structure
* Use caching per component
