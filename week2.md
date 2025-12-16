# Week 2: Security Planning & Testing Methodology

<div style="display:flex; gap:10px; margin-top:20px;">

  <a href="index.html" style="
     background:#f4d7e3;
     padding:10px 18px;
     color:#5a3a45;
     border-radius:8px;
     text-decoration:none;
     border:1px solid #e7bdcc;
     font-weight:600;">
      Back to Home
  </a>

  <a href="week3.html" style="
     background:#f4d7e3;
     padding:10px 18px;
     color:#5a3a45;
     border-radius:8px;
     text-decoration:none;
     border:1px solid #e7bdcc;
     font-weight:600;">
     ➡ Next: Week 3
  </a>

</div>

---

## 1. Performance Testing Plan (Remote Monitoring & Approach)

This week focuses on designing how the performance of the Ubuntu Server VM will be tested and monitored **remotely** from the Debian workstation.
The goal is to understand how the operating system behaves under different workloads without using the VirtualBox console directly, reflecting a real-world remote administration model.

---

### 1.1 Objectives

* Measure CPU, memory, disk I/O, and network usage on the Ubuntu Server
* Observe how resource usage changes under different workloads (idle, light, heavy)
* Perform all administration and monitoring strictly over SSH from the Debian workstation
* Produce measurements that can be reused in **Week 6** for performance graphs and analysis

---

### 1.2 Remote Monitoring Methodology

All monitoring will be carried out from the Debian workstation using SSH to connect to the Ubuntu Server:

```bash
ssh eva@192.168.1.221
```

Planned monitoring tools on the server include:

* **top / htop** – live view of CPU, memory, and processes
* **vmstat** – CPU usage, context switches, interrupts
* **iostat** (from `sysstat`) – disk I/O throughput and utilisation
* **ss / netstat** – active network connections and listening ports
* **ping** and possibly **iperf3** – network latency and throughput testing

To align with later phases, a remote monitoring script will be created in **Week 5** (`monitor-server.sh`) with the following behaviour:

* Runs on the Debian workstation
* Connects to the server via SSH
* Collects metrics such as:

  * `vmstat 1 10`
  * `iostat`
  * `free -h`
* Saves results to a timestamped log file (e.g. `logs/server-metrics-YYYYMMDD-HHMM.txt`)

This approach allows the same script to be reused consistently in **Week 6**, avoiding manual command repetition.

---

### 1.3 Planned Test Scenarios

The same measurement process will be applied under multiple scenarios:

#### Baseline (Idle System)

* Only essential services running (SSH, Apache)
* Collect CPU, memory, disk, and network metrics using `vmstat` and `iostat`

#### Light Web Load

* Use a browser or simple HTTP client from Debian to repeatedly request an Apache test page
* Compare CPU and memory usage against the idle baseline

#### CPU-Intensive Workload

* In Weeks 3–4, run a CPU-heavy task (e.g. stress tool or compression)
* Monitor core usage with `htop` and `vmstat`

#### I/O-Intensive Workload

* Generate disk activity using tools such as `dd` or file copy
* Observe disk throughput and utilisation via `iostat`

#### Combined Load

* Web traffic combined with a background CPU or disk-heavy job
* Identify which resource becomes the bottleneck first

For each scenario, the following metrics will be collected:

* Average and peak CPU usage
* Memory usage (used, free, cached)
* Disk utilisation (%) and throughput
* Network usage (traffic levels and open connections)

These results will later be organised into tables and visualised as graphs in **Week 6**, as required by the coursework brief.

---

### 1.4 Trade-offs and Limitations

* As this is a single VM running on a laptop, results are influenced by host system load
* SSH-based monitoring introduces minor CPU and network overhead, which is acceptable and realistic
* Preference is given to lightweight, CLI-based tools to keep the server headless and efficient

---

## 2. Security Configuration Checklist (Planned Baseline)

This section defines the **security baseline** planned for completion by the end of Phases 4 and 5.
At this stage, it remains a plan and checklist rather than a full implementation.

| Area                          | Planned Configuration                                                                    | Rationale                                       |
| ----------------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------- |
| **SSH hardening**             | Key-based authentication, disable password login, disable root SSH login, restrict users | Reduce brute-force and credential-based attacks |
| **Firewall (UFW)**            | Default deny incoming, allow SSH only from Debian IP, allow HTTP (80)                    | Enforce least-privilege network access          |
| **Mandatory Access Control**  | Enable AppArmor and enforce profiles for Apache and critical services                    | Limit impact of service compromise              |
| **Automatic updates**         | Enable unattended security updates                                                       | Reduce exposure to known vulnerabilities        |
| **User privilege management** | Non-root admin user with sudo, disable direct root login                                 | Minimise damage from account compromise         |
| **Network security**          | Restrict listening services and ports                                                    | Reduce attack surface and lateral movement      |

This checklist reflects a **defence-in-depth strategy**, combining preventive, detective, and containment controls appropriate for a small Linux server environment.

---

### 2.1 SSH Hardening (Planned)

Planned changes in `/etc/ssh/sshd_config` include:

```bash
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

SSH access will be restricted using `AllowUsers` to limit login to a specific user from the Debian workstation.

Evidence to be provided in **Week 4** will include:

* Before and after `sshd_config` snippets
* Successful key-based SSH login from Debian
* Failed attempts when using password authentication

---

### 2.2 Firewall Configuration (Planned with UFW)

Planned firewall rules:

* Default policy: deny incoming, allow outgoing
* Allow SSH only from the Debian workstation IP (e.g. `192.168.1.183`)
* Allow HTTP from the local network
* Deny all other inbound traffic

Firewall status will later be documented using:

```bash
sudo ufw status verbose
```

---

### 2.3 Mandatory Access Control (AppArmor)

The plan includes:

* Verifying AppArmor is in enforcing mode on Ubuntu Server
* Ensuring Apache (`/usr/sbin/apache2`) runs under an enforcing profile
* Reviewing logs (`journalctl` or `/var/log/syslog`) for blocked access attempts

This will be formally documented in **Phase 5**.

---

### 2.4 Automatic Updates

Planned actions:

* Install and configure `unattended-upgrades`
* Enable automatic security updates and periodic checks
* Record configuration from `/etc/apt/apt.conf.d/50unattended-upgrades` and relevant logs

This reduces the window of exposure to known vulnerabilities.

---

### 2.5 User Privilege Management

Planned strategy:

* Use a non-root user (e.g. `eva`) with sudo access
* Avoid direct root login
* Use `sudo` for privileged operations to maintain an audit trail
* Prefer dedicated service accounts where possible

---

### 2.6 Network Security

Planned network-level controls:

* Verify only required services are listening using:

```bash
ss -tuln
```

* Keep all services confined to the local virtual network
* Use tools such as `nmap` later to confirm that only expected ports are open

---

## 3. Threat Model (Week 2 Planning)

This section identifies key threats relevant to the virtual environment and outlines planned mitigation strategies.

---

### 3.1 Scope and Assets

Key assets to protect include:

* Ubuntu Server configuration (SSH, Apache, firewall, AppArmor)
* Server availability under load
* Integrity of configuration files and log data
* Trust relationship between workstation and server (SSH keys, access controls)

---

### 3.2 Threats and Mitigations

| Threat                             | Description                              | Likelihood | Impact      | Planned Mitigations                                                                          |
| ---------------------------------- | ---------------------------------------- | ---------- | ----------- | -------------------------------------------------------------------------------------------- |
| **Brute-force SSH attacks**        | Repeated password guessing on port 22    | Medium     | High        | Key-based auth, disable passwords, disable root login, restrict SSH by IP, consider Fail2Ban |
| **Web service exploitation**       | Vulnerabilities in Apache or future apps | Medium     | High        | Regular updates, AppArmor confinement, minimal modules, log monitoring                       |
| **Administrator misconfiguration** | Firewall or SSH errors                   | High       | Medium–High | Config backups, test changes in parallel SSH sessions, clear documentation                   |
| **Denial-of-Service from load**    | CPU/RAM/I/O exhaustion                   | Medium     | Medium      | Gradual testing, continuous monitoring, avoid unnecessary services                           |
| **Lateral movement**               | Pivoting between VMs                     | Low–Medium | High        | Minimal open ports, patched systems, restricted SSH keys, no shared folders                  |

This threat model will be refined in later weeks as additional services and security tools are introduced.

---

## 4. Reflection (Week 2)

This week focused on **planning rather than immediate implementation**.
Designing the performance testing strategy early will simplify Week 6, as the metrics, tools, and test structure are already defined.

Similarly, developing the security checklist and threat model provides a clear roadmap for Weeks 4 and 5, where SSH hardening, firewall rules, AppArmor configuration, automatic updates, and monitoring scripts will be implemented.

This planning phase also highlighted key trade-offs, such as balancing stronger security (e.g. disabling password authentication) against usability and the risk of accidental lockout, reflecting real-world operating system administration challenges.
