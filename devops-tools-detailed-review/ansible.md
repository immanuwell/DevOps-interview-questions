---
title: "Ansible"
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

# **Ansible core concepts**

## **Core Concepts**

* **Agentless**: Works over SSH/WinRM. No agent installed on target hosts.
* **Idempotent**: Running the same play multiple times leads to the same state.
* **Declarative**: You describe the desired final state; modules handle the how.
* **YAML-based Playbooks**: Tasks written as YAML documents.
* **Inventory**: List of hosts — static file, dynamic script, cloud plugin.
* **Modules**: Reusable units of work (`apt`, `yum`, `copy`, `template`, `service`).
* **Roles**: Standard structure for reusable automation (tasks, handlers, templates).
* **Facts**: System information gathered automatically (`ansible_facts`).

***

## **Inventory**

* Default: `/etc/ansible/hosts`.
* Supports **groups**, **nested groups**, **variables per host/group**.
* Supports **dynamic inventory** (AWS, GCP, Azure, Kubernetes…).
* Host vars override group vars → group vars override global vars.

***

## **Playbooks**

* YAML file describing plays: hosts + tasks.
* Each **task** uses a module.
* Use **handlers** for notification-based tasks (restart service only when needed).
* Use **when** for conditionals.
* Use **loops** for repetitive tasks.
* Use **become: yes** for privilege escalation.

***

## **Variables**

* Several places: playbooks, inventory, `group_vars/`, `host_vars/`, vars\_files, extra vars.
* **Precedence**: extra vars > task vars > block vars > play vars > inventory.
* Use `{{ variable }}` syntax.
* **Jinja2 templating** for complex expressions.

***

## **Roles**

* Roles are reusable automation units.
* Standard directories: `tasks`, `handlers`, `templates`, `files`, `vars`, `defaults`, `meta`.
* **defaults** have lowest priority; **vars** have higher.
* Galaxy roles installed using `ansible-galaxy install`.

***

## **Modules**

Most common modules for interviews:

* **Package management**: `apt`, `yum`, `package`.
* **Service control**: `service`, `systemd`.
* **User management**: `user`, `group`.
* **File ops**: `copy`, `file`, `template`, `synchronize`, `unarchive`.
* **Networking**: `uri`, `iptables`, `docker_*`, `k8s`.
* **System**: `shell`, `command`, `raw` (last resort).

**Key difference:**

* `shell` → runs through shell (supports pipes, redirects).
* `command` → no shell, safer, no pipes.

***

## **Handlers**

* Run only when notified from a task.
* Used for restart/reload actions.
* Executed at the end of a play by default.

***

## **Templates**

* Use `template:` + Jinja2.
* Supports variables, filters, loops.
* Useful for dynamic configs: Nginx, HAProxy, Prometheus, etc.

***

## **Ansible Vault**

* Encrypts sensitive files/vars.
* Use `ansible-vault encrypt file.yml`.
* Use `--ask-vault-pass` or `--vault-password-file`.
* Vault files can be used inside playbooks like normal vars.

***

## **Execution**

* `ansible all -m ping -i inventory.ini` → test hosts.
* `ansible-playbook site.yml` → run playbook.
* `ansible-playbook site.yml --check` → dry-run (no changes).
* `ansible-playbook site.yml --diff` → show config changes.
* `ansible-playbook site.yml -e var=value` → extra vars.
* `ansible-galaxy init role_name` → create a new role skeleton.

***

## **Idempotency**

* Modules should report **changed** only when state changed.
* Using `shell` or `command` breaks idempotency unless manually controlled (`creates=`, `removes=`).

***

## **Common Interview Questions**

* Difference between **Playbook** and **Role**.
* Difference between **Vars**, **defaults**, **extra vars**, **host\_vars/group\_vars**.
* How Ansible achieves **idempotency**.
* When to use **shell** vs **command**.
* What is **Ansible Vault** and how to use it.
* How **handlers** work and why they are important.
* Static vs **dynamic inventory**.
* How to structure a large project (roles, group\_vars, host\_vars).
* How to troubleshoot Ansible (verbosity: `-vvv`).
* What is **check mode** (`--check`) and why it's critical.

***

## **Best Practices**

* Always use roles for non-trivial projects.
* Keep secrets in Vault.
* Avoid `shell` unless necessary.
* Use `tags:` to speed up runs.
* Use `become: yes` instead of `sudo` in commands.
* Keep playbooks small; reuse roles.
* Use dynamic inventory for cloud environments.
* Test with `--check` before you break production.

---

# **Ansible interview questions**

## **1. What is idempotency in Ansible?**

Idempotency means running a task multiple times results in the **same final state**; nothing changes unless required. Most core modules are idempotent.

---

## **2. How does Ansible ensure idempotency?**

Through **state-based modules** (e.g., `state=present`), attribute checks, and comparison of remote state before applying changes.

---

## **3. What is the difference between `vars`, `vars_files`, and `host_vars`/`group_vars`?**

* `vars` → inline variables inside playbook
* `vars_files` → load variables from external YAML
* `host_vars` / `group_vars` → automatically loaded based on inventory grouping

---

## **4. What is `ansible_facts`?**

Automatically collected system information (RAM, OS, interfaces, CPU, etc.). Accessible via `ansible_facts['hostname']` or shorthand `ansible_hostname`.

---

## **5. How does Ansible Vault work?**

Encrypts sensitive data (passwords, API keys) using AES256. Decrypted only at runtime using a vault password or vault ID.

---

## **6. What is the main difference between `command` and `shell`?**

* `command`: safer, no shell processing (no pipes, redirects, variables)
* `shell`: executes through `/bin/sh` — required for pipes, wildcards, redirects

---

## **7. When should you use `handlers`?**

Use handlers for **actions that should run only when notified**, e.g. restarting services after config changes.

---

## **8. What is the purpose of `become` and privilege escalation?**

`become: true` allows tasks to run as another user (usually root). Controlled via `become_user`, `become_method`.

---

## **9. What is a Role in Ansible?**

A structured way to organize code into independent components:

* tasks/
* templates/
* files/
* handlers/
* vars/
* defaults/
* meta/

Promotes reusability and clean structure.

---

## **10. What is the difference between `vars` and `defaults` inside roles?**

* `defaults/main.yml` → lowest priority variables
* `vars/main.yml` → high priority variables (harder to override)

---

## **11. What is `when` and how does it work?**

Conditional execution. Evaluates Jinja2 expressions:

```yaml
when: ansible_os_family == "Debian"
```

Supported at task, block, or role level.

---

## **12. How do `blocks` help with error handling?**

`block`, `rescue`, `always` allow try/catch/finally-style logic:

* Block: tasks to try
* Rescue: run when block fails
* Always: run regardless

---

## **13. What is the difference between `with_items` and `loop`?**

`loop:` is modern and more flexible, supports complex data structures.
`with_items:` is legacy and deprecated in many cases.

---

## **14. How does `check_mode` work?**

Runs tasks in **dry-run**, showing changes without applying them. Useful for CI/CD validation.

---

## **15. What is `delegate_to` in Ansible?**

Runs a task **on a different host** than the one the play is targeting. Used for:

* Load balancer updates
* API calls
* Orchestration from a controller host

---

## **16. What is the difference between Pull and Push mode in Ansible?**

* **Push (default)**: Controller pushes tasks to nodes via SSH.
* **Pull (`ansible-pull`)**: Nodes pull playbooks from Git — useful for autoscaling fleets.

---

## **17. How can you optimize Ansible performance for large inventories?**

* Use **`forks`** to increase parallelism
* Disable fact gathering (`gather_facts: false`) if unnecessary
* Use **SSH pipelining**
* Use `strategy: free` for faster asynchronous execution
* Reuse SSH connections (`ControlPersist`)

---

## **18. What is `strategy: free` vs `strategy: linear`?**

* **linear**: tasks run in order for all hosts (default)
* **free**: hosts are not synchronized, tasks run as soon as each host is ready → faster parallelism

---

## **19. What is the purpose of `meta:` operations?**

Special control actions:

* `meta: flush_handlers`
* `meta: end_play`
* `meta: clear_facts`

Used for controlling playbook flow.

---

## **20. What is the difference between `include_tasks`, `import_tasks`, and `include_role`?**

* `import_tasks`: **static**, loaded at parse time
* `include_tasks`: **dynamic**, loaded at runtime, supports conditionals
* `include_role`: imports a role dynamically inside a task
