---
title: "GitHub Actions"
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


# **GitHub Actions**

## **Core Concepts**

* GitHub Actions is a CI/CD platform integrated into GitHub.
* Workflows are defined in YAML under `.github/workflows/`.
* Workflows run on triggers (events), jobs, and steps.
* Runners execute jobs — GitHub-hosted or self-hosted.
* Reusable workflows allow DRY pipelines across repos.

---

## **Workflows**

* A workflow = set of jobs triggered by events.
* Common triggers:

  * `push`, `pull_request`, `workflow_dispatch`, `schedule (cron)`, `release`, `issue_comment`.
* You can define multiple workflows per repository.

---

## **Jobs**

* Jobs run in parallel by default.
* Use `needs:` to define dependencies (sequential execution).
* Each job runs on its own fresh runner VM.
* Common runners: `ubuntu-latest`, `windows-latest`, `macos-latest`, or self-hosted.

---

## **Steps**

* Steps run inside jobs.
* Steps can run shell commands or actions (reusable logic).
* Steps share workspace but not environment by default unless `env:` is set.

---

## **Actions**

* Reusable units of logic.
* Types:

  * **Docker actions** — packaged in containers.
  * **JavaScript actions** — fastest, native execution.
  * **Composite actions** — combine multiple shell/steps.
* Use public actions from GitHub Marketplace.

---

## **Secrets & Security**

* Store sensitive data in `Settings → Secrets and Variables`.
* Available as `secrets.MY_SECRET`.
* Secrets are masked in logs.
* Avoid secrets in `pull_request` events from forks (security risk).
* Use `GITHUB_TOKEN` for auth; permissions configurable via `permissions:` block.

---

## **Artifacts & Caching**

* **Artifacts**: upload/download build outputs using `actions/upload-artifact`.
* **Caching**: speed up builds with `actions/cache`.
* Typical cache targets: dependencies, Docker layers, build results.
* Cache keys should be deterministic and versioned.

---

## **Matrix Builds**

* Run job in parallel with different parameters.
* Example: multiple OS versions, multiple Python/Go/Node versions.
* Use `strategy.matrix:`.

---

## **Concurrency & Limits**

* Use `concurrency:` to prevent duplicate builds (e.g., cancel old runs).
* GitHub-hosted runners have execution time and resource limits.
* Self-hosted runners allow full customization but require maintenance.

---

## **Reusable Workflows**

* Located in `.github/workflows/` of any repo.
* Called via `workflow_call`.
* Useful for shared CI templates across org.

---

## **Environment Protection**

* Use environments for deployment (dev/stage/prod).
* Add manual approvals, wait timers, or restricted secrets.
* Can require specific reviewers for protected deployments.

---

## **Common Interview Questions**

* Explain workflow → job → step hierarchy.
* Difference between GitHub-hosted and self-hosted runners.
* How to secure secrets on Actions?
* How to trigger workflows manually?
* How matrix builds are implemented?
* How to reuse workflows across repositories?
* How to handle long-running deployments?
* How caching works and typical cache patterns.
* How to restrict deployments to production with approvals.
* How to avoid running workflows twice (push + PR on same commit).
* How to handle monorepos with conditional paths (`paths`, `paths-ignore`).

---

## **Useful YAML Snippets**

### **Basic workflow**

```yaml
name: CI
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: make test
```

### **Matrix build**

```yaml
strategy:
  matrix:
    go-version: [1.21, 1.22]
```

### **Caching example**

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.cache/go-build
    key: ${{ runner.os }}-go-${{ hashFiles('**/*.go') }}
```

### **Reusable workflow**

```yaml
on:
  workflow_call:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Reusable workflow"
```
