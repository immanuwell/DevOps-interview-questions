---
title: "Linux"
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

# **Linux core concepts**

## **1. Linux Architecture**

* Everything is a file (devices, sockets, pipes).
* Kernel handles process scheduling, memory, I/O, networking.
* System calls bridge user space ↔ kernel space (`strace` shows them).
* Runlevels replaced by systemd targets (multi-user, graphical).

---

## **2. Processes & Systemd**

* `ps aux`, `top`, `htop`, `atop` → inspect processes & resource usage.
* `systemctl start/stop/status/enable/disable/restart unit`.
* Signals: `SIGTERM` (15, graceful), `SIGKILL` (9, force), `SIGINT` (2, Ctrl+C).
* `nice` & `renice` → CPU scheduling priorities.
* `nohup` & `&` → run tasks in background.

---

## **3. Filesystems**

* Common FS: ext4, xfs, btrfs, zfs.
* `df -h` → disk usage.
* `du -sh *` → directory usage.
* `lsblk`, `blkid`, `mount`, `umount`.
* `/etc/fstab` → persistent mounts.
* Inodes track metadata; running out of inodes breaks file creation.

---

## **4. Permissions & Ownership**

* `rwx` model for user/group/other.
* `chmod`, `chown`, `chgrp`.
* Special bits:

  * **SUID (4)** → run as owner.
  * **SGID (2)** → run as group.
  * **Sticky bit (1)** → only owner can delete.
* ACLs: `getfacl`, `setfacl`.

---

## **5. Networking**

* `/etc/hosts`, `/etc/resolv.conf`, `/etc/hostname`.
* `ip a`, `ip r`, `ip link`, `ip neigh`, `ip -s link`.
* `ss -tulnp` → open ports; replacement for netstat.
* `curl -v`, `wget`, `nc`, `dig`, `tcpdump`, `traceroute` → debugging.
* Firewalls: `iptables`, `nftables`, `ufw`, `firewalld`.

---

## **6. System Monitoring & Troubleshooting**

* CPU/mem: `top`, `htop`, `free -h`, `vmstat`, `sar`.
* Disk I/O: `iotop`, `iostat`, `atop`, `dmesg`.
* Logs: `/var/log`, `journalctl -u unit --since ...`.
* Kernel messages: `dmesg`.
* Bottleneck debugging basics:
  CPU → `top`
  Disk → `iotop`
  RAM → `free`
  Network → `ss`, `tcpdump`
  File locks → `lsof`

---

## **7. Users & Authentication**

* Users: `/etc/passwd`, `/etc/shadow`.
* Groups: `/etc/group`.
* Add/delete: `useradd`, `userdel`, `usermod`, `passwd`.
* sudo rules: `/etc/sudoers`, `visudo`.

---

## **8. Packages & Repositories**

### Debian/Ubuntu:

* `apt update`, `apt upgrade`, `apt install package`.

### RHEL/CentOS/Rocky:

* `dnf install`, `dnf update`, `dnf repoquery`, `rpm -qi`.

### Universal:

* `systemctl daemon-reload` after service unit change.

---

## **9. Disk & Storage Management**

* LVM: `pvcreate`, `vgcreate`, `lvcreate`, `lvextend`, `resize2fs`.
* RAID: `mdadm --create`, `mdadm --detail`.
* Swap: `mkswap`, `/etc/fstab`, `swapon`, `swapoff`.

---

## **10. Networking Services**

* SSH: `/etc/ssh/sshd_config`; keys in `~/.ssh/`.
* SCP/SFTP: secure transfers.
* OpenSSH key types: RSA, ED25519 (preferred).

---

## **11. Bash & Shell Scripting Essentials**

* `set -euo pipefail` → safe defaults.
* Shebang: `#!/usr/bin/env bash`.
* Variables: `VAR=value`.
* Subs: `$(cmd)` preferred over backticks.
* Useful tools: `grep`, `sed`, `awk`, `cut`, `sort`, `uniq`, `tr`.

---

## **12. Compression & Archiving**

* tar: `tar -czvf file.tar.gz dir/`
* zip/unzip: `zip -r`, `unzip`
* gz, bz2, xz: `gzip`, `bzip2`, `xz`.

---

## **13. Security Basics**

* Disable root ssh login (`PermitRootLogin no`).
* Use SSH keys, not passwords.
* File integrity: `sha256sum`.
* Process capabilities: `getcap`, `setcap`.
* SELinux:

  * Modes: enforcing/permissive/disabled
  * Tools: `getenforce`, `setenforce`, `semanage`, `restorecon`

---

## **14. Containers & Runtime**

(Essential Linux-level knowledge)

* Namespaces: isolate processes (PID, NET, MNT, IPC, UTS, USER).
* Cgroups: limit CPU/RAM/IO.
* `ctr` and `crictl` for containerd debugging.
* OverlayFS: used by Docker/K8s for layered images.

---

## **15. Kernel & Boot**

* GRUB2 handles boot.
* Kernel tuned via `/proc/sys/*` and `/etc/sysctl.conf`.
* Common sysctl parameters:

  * `net.ipv4.ip_forward`
  * `fs.inotify.max_user_watches`
  * `net.core.somaxconn`

---

## **16. SSH, Tunnels, & Networking Tricks**

* Forward local port:
  `ssh -L 8080:localhost:80 user@host`
* Reverse tunnel:
  `ssh -R 9000:localhost:22 user@remote`.
* Copy key: `ssh-copy-id`.

---

## **17. Cron Jobs**

* User cron: `crontab -e`
* System cron: `/etc/crontab`, `/etc/cron.d/`
* Timing format:
  `* * * * *` → min hour day month weekday.

---

## **18. File & Directory Utilities**

* `find /path -type f -mtime -1`
* `rsync -avh --progress src/ dst/`
* `scp`, `sftp`
* `stat`, `file`

---

## **19. Common Interview Scenarios**

* Debug high CPU → `top`, `pidstat -u`, `strace`
* Debug high I/O → `iotop`, `iostat`
* Debug memory leak → `top`, `smem`, `pmap`
* Debug network → `ss`, `tcpdump`, `curl -v`
* DNS issues → `dig`, `/etc/resolv.conf`
* System slow → check: `dmesg`, `journalctl`, load average.

---

# **Linux interview questions**

## **1. What is the difference between a process and a thread in Linux?**

A **process** has its own memory space, file descriptors, and system resources.
A **thread** shares the parent process memory and resources.
Threads are cheaper to create, switch, and communicate between.

---

## **2. What is a zombie process?**

A process that has **finished execution** but still has an entry in the process table because its parent hasn’t read its exit status (`wait()`).
It consumes **no CPU**, but too many zombies waste PID space.

---

## **3. What is a daemon? How is it different from a service?**

A **daemon** is a background process not tied to a terminal (e.g., `sshd`).
A **service** is a daemon managed by a service manager like **systemd**, with lifecycle control, dependencies, and monitoring.

---

## **4. Explain the role of the kernel in Linux.**

The kernel manages **hardware**, **memory**, **process scheduling**, **permissions**, and **system calls**.
User applications interact with hardware only through the kernel.

---

## **5. What are Linux namespaces?**

Namespaces isolate system resources for containers:

* `pid` – process IDs
* `net` – network interfaces
* `mnt` – mounts
* `ipc` – message queues
* `uts` – hostname
* `user` – UID/GID mappings
  This provides container-level isolation.

---

## **6. What is cgroups and why is it used?**

**Control groups** limit and control resource usage: CPU, memory, IO, pids, etc.
Used by **Docker**, **Kubernetes**, systemd.

---

## **7. Explain the difference between hard links and soft links.**

Hard link → direct pointer to inode, same file, must be on **same filesystem**.
Soft link → symbolic reference (like shortcut), can cross filesystems, breaks if target removed.

---

## **8. What happens when you run a command in Linux?**

1. Shell parses input
2. Shell searches `PATH`
3. Forks child process
4. Child calls `exec()`
5. Kernel loads binary into memory
6. Process runs until exit
7. Parent collects exit status

---

## **9. What is the difference between `cron`, `anacron`, and `systemd timers`?**

* **cron** – runs scheduled tasks but only when machine is on
* **anacron** – runs missed jobs after machine boots
* **systemd timers** – more powerful; logs, dependencies, delays, accuracy, randomization

---

## **10. What is a file descriptor?**

An integer that refers to an open file, socket, or pipe.
Standard ones:

* 0 → stdin
* 1 → stdout
* 2 → stderr

---

## **11. What is inode and what information does it store?**

Inode stores metadata:

* permissions
* owner
* timestamps
* pointers to data blocks
  **Does NOT store filename.**
  Filename is stored in the directory entry.

---

## **12. What is the difference between `load average` and CPU usage?**

* **CPU usage** = % CPU is busy
* **Load average** = number of processes waiting for CPU or I/O
  High load average with low CPU = **I/O bottleneck**

---

## **13. How does Linux handle memory allocation (OOM killer)?**

When memory is critically low, the kernel chooses a victim to kill based on **OOM score**, which depends on memory usage and process importance.

---

## **14. What is the difference between `fork()` and `exec()`?**

`fork()` → creates a copy of the process
`exec()` → replaces process memory with a new program
Typically used together to spawn programs.

---

## **15. What is the role of `/proc` filesystem?**

Pseudo-filesystem exposing kernel and process info:

* `/proc/cpuinfo`
* `/proc/meminfo`
* `/proc/<pid>/status`
  Used for debugging and monitoring.

---

## **16. Explain what `strace` does.**

Traces system calls and signals.
Used for debugging: missing files, permissions, syscalls, network connections.

---

## **17. What does `nice` and `renice` do?**

Controls process scheduling priority.
Higher niceness → lower priority.
Range: **-20 (highest priority) to 19 (lowest).**

---

## **18. Explain "copy-on-write" in Linux.**

When a process forks, parent and child share pages.
Memory pages are only duplicated if one modifies them.
Saves memory and speeds up process creation.

---

## **19. What is `SELinux` and how does it work?**

Mandatory access control (MAC) enforcing rules on processes using labels.
Policies restrict what a process *can* do even if it’s root.
Modes: `enforcing`, `permissive`, `disabled`.

---

## **20. Explain what happens when Linux runs out of RAM and swap.**

Kernel triggers:

1. **Page cache reclaim**
2. **Swap out inactive pages**
3. **OOM killer** to terminate largest offender
   If swap=0 → OOM killer triggers earlier.

