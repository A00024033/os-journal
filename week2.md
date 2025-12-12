Week 2 – Security Planning & Testing Methodology

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

1. Performance Testing Plan (Remote Monitoring & Approach)

This week I designed how I will test and monitor the performance of my Ubuntu Server VM remotely from the Debian workstation. The goal is to understand how the operating system behaves under different workloads without ever using the direct VirtualBox console on the server.

1.1 Objectives

Measure CPU, memory, disk I/O and network usage on the Ubuntu Server.

Observe how resource usage changes under different types of workloads (idle, light, heavy).

Keep all administration and monitoring strictly over SSH from the Debian workstation, matching the real-world “remote admin” model described in the brief. 

Produce measurements that I can reuse in Week 6 for performance graphs and analysis.

1.2 Remote Monitoring Methodology

All monitoring will be done from the Debian workstation VM using SSH to connect to the Ubuntu Server:

ssh eva@192.168.1.221

Planned monitoring tools on the server:

top / htop – live view of CPU, memory and processes

vmstat – CPU usage, context switches, interrupts

iostat (from sysstat) – disk I/O throughput and utilisation

ss / netstat – active network connections and listening ports

ping and possibly iperf3 – basic network latency / throughput between workstation and server

To keep this aligned with later phases, I also plan to create a remote monitoring script in Week 5 (monitor-server.sh) that will:

Run on the Debian workstation

Connect to the server via SSH

Collect metrics (e.g. vmstat 1 10, iostat, free -h)

Save results into a timestamped log file (e.g. logs/server-metrics-YYYYMMDD-HHMM.txt) 

This allows me to re-use the same script for multiple tests in Week 6 instead of typing commands manually every time.

1.3 Planned Test Scenarios

I will run the same measurement steps under several scenarios:

Baseline (Idle System)

Only essential services running (SSH, Apache).

Collect CPU, memory, disk, and network metrics for a few minutes using vmstat and iostat.

Light Web Load

Use a browser or a simple HTTP client from Debian to request a test page on Apache repeatedly.

Record how CPU and memory respond compared to idle.

CPU-intensive Workload

Later in Week 3–4 I will pick a CPU-heavy application (e.g. stress test tool or compression).

Run it while monitoring with htop, vmstat, and note how many cores are saturated.

I/O-intensive Workload

Use a tool like dd or file copy to generate disk writes.

Observe disk utilisation and throughput via iostat.

Combined Load

Web traffic + a background job (CPU or disk heavy) at the same time.

This helps identify which resource becomes the bottleneck first.

For each scenario I plan to collect:

Average and peak CPU usage

Memory usage (used, free, cached)

Disk utilisation (%) and throughput

Network usage (traffic and open connections)

These measurements will later be placed into a performance table and turned into graphs in Week 6, as required by the brief. 

1.4 Trade-offs and Limitations

Because this is a single VM on a laptop, results are influenced by host load, not just the VM.

SSH monitoring itself uses a small amount of CPU and network bandwidth, but this overhead is acceptable and realistic for remote administration.

I am prioritising simple, CLI-based tools over heavy GUIs to keep the server lightweight and aligned with the “headless” requirement. 

2. Security Configuration Checklist (Planned Baseline)

This section sets out the security baseline I intend to reach by the end of Phases 4 and 5. For Week 2, it remains a plan and checklist, not a full implementation.

| Area | Planned Configuration | Rationale |
|------|-----------------------|-----------|
| **SSH hardening** | Enable key-based authentication, disable password login, disable root SSH login, limit users | Reduce risk of brute-force and credential attacks on SSH |
| **Firewall (UFW)** | Default deny incoming traffic, allow SSH only from Debian workstation IP, allow HTTP (80) | Enforce least-privilege network access and reduce exposed attack surface |
| **Mandatory Access Control** | Enable AppArmor and ensure Apache and critical services run with enforcing profiles | Prevent services from accessing unnecessary files or resources, limiting impact of compromise |
| **Automatic updates** | Configure unattended security updates and periodic package refresh | Ensure the system is regularly patched against known vulnerabilities |
| **User privilege management** | Use a non-root administrative user with sudo and disable direct root login | Reduce damage if a single account is compromised |
| **Network security** | Restrict services to required interfaces and ports, disable unnecessary listeners | Reduce entry points and limit lateral movement within the virtual network |

This checklist reflects a layered defence-in-depth strategy, combining preventive, detective, and containment controls appropriate for a small production Linux server environment.

2.1 SSH Hardening (Planned)

Planned changes in /etc/ssh/sshd_config:

PermitRootLogin no

PasswordAuthentication no (once key-based auth is working)

PubkeyAuthentication yes

Use AllowUsers to restrict SSH access to a specific user from the Debian workstation.

Later evidence (Week 4) will include:

Before/after sshd_config snippets

ssh -i connection from Debian to Ubuntu using keys

Failed attempts when trying password login.

2.2 Firewall Configuration (Planned with UFW)

Rules I plan to enforce:

Default: deny incoming, allow outgoing

Allow SSH from only the Debian workstation IP (e.g. 192.168.1.183)

Allow HTTP from the local network (for web testing)

Deny all other inbound traffic.

Firewall state will be documented using:

sudo ufw status verbose


so I can show the full ruleset and default policies clearly in later weeks.

2.3 Mandatory Access Control (AppArmor)

The plan is to:

Verify AppArmor is in enforcing mode on Ubuntu Server.

Ensure there is an active profile for Apache (/usr/sbin/apache2) and any other key services I use.

Use the logs (e.g. journalctl or /var/log/syslog) to see if AppArmor blocks suspicious access attempts.

This ties into Phase 5 when I formally document AppArmor configuration and monitoring. 

2.4 Automatic Updates

I plan to:

Install and configure unattended-upgrades on Ubuntu Server.

Enable automatic security updates and periodic checks.

Record configuration from /etc/apt/apt.conf.d/50unattended-upgrades and show log entries proving it ran.

This reduces the window where known vulnerabilities are unpatched, especially if I am not manually updating the server every day.

2.5 User Privilege Management

Planned user and privilege strategy:

Use a non-root user (e.g. eva) with sudo access for administration.

Avoid logging in as root directly.

Use sudo for privileged actions to keep a clear audit trail.

Where possible, use dedicated service accounts instead of running services as root.

2.6 Network Security

At network level I plan to:

Ensure only necessary services are listening (ss -tuln to list).

Avoid exposing any ports to the wider internet by keeping everything inside the local bridged / host-only network. 

Later, use tools like nmap within the VirtualBox environment to verify that only expected ports are open.

3. Threat Model (Week 2 Planning)

In this section I identify key threats relevant to my small virtual environment and outline mitigation strategies that align with the checklist above.

3.1 Scope & Assets

The main assets I want to protect are:

The Ubuntu Server configuration (SSH, Apache, firewall, AppArmor)

The availability of the server under load (no accidental DoS)

The integrity of configuration files and log data used in my journal

The workstation–server trust relationship (SSH keys, restricted access)

3.2 Threats and Mitigations

| Threat                                          | Description                                                                                                   | Likelihood    | Impact                                                                                              | Planned Mitigations                                                                                                                                           |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Brute-force attacks on SSH**                  | An attacker on the same LAN or misconfigured network repeatedly guesses passwords on port 22.                 | Medium        | High – full server compromise if password is weak.                                                  | Enforce key-based auth, disable password auth, disable root login, restrict SSH to Debian IP via firewall, consider Fail2Ban in later weeks.                  |
| **Exploitation of a vulnerable web service**    | Apache or a future web app could contain vulnerabilities that allow code execution or data access.            | Medium        | High – attacker could run commands as the web server user.                                          | Keep packages updated, use AppArmor to confine Apache, minimise modules, monitor logs for suspicious requests.                                                |
| **Misconfiguration by the administrator (me)**  | A mistake in firewall or SSH config could lock me out or weaken security.                                     | High          | Medium to High. Losing SSH access would block further work; weakening rules could open extra ports. | Make backups of config files before changes, test changes via a second SSH session, document before/after configs in the journal.                             |
| **Denial-of-Service from heavy workloads**      | Stress tests or badly chosen applications might exhaust CPU/RAM or disk I/O and make the server unresponsive. | Medium        | Medium – affects availability and testing reliability.                                              | Start with baseline tests, gradually increase load, use monitoring to stop tests before total exhaustion, avoid running unnecessary extra services.           |
| **Lateral movement inside the virtual network** | If one VM is compromised, the attacker might pivot to the other VM or host.                                   | Low to Medium | High – compromise of workstation or host.                                                           | Use host-based firewalls, only open essential ports, avoid shared folders between host and VMs, keep workstation patched and restrict SSH keys to the server. |

This initial threat model will be refined in later weeks when I add more services, scripts, and security tools (e.g. Fail2Ban, Lynis, nmap scans). The goal is to show that my security configuration is driven by concrete threats, not just a random list of commands.

4. Reflection (Week 2)

This week was about planning, not just typing commands. Designing the performance testing plan early should make Week 6 much easier, because I already know:

Which metrics I care about

Which tools I will use

How to structure tests and collect data

Similarly, creating the security checklist and threat model now gives a clear roadmap for Weeks 4 and 5, where I will actually implement SSH hardening, firewall rules, AppArmor, automatic updates, and monitoring scripts.

It also made me think about trade-offs: for example, disabling password authentication improves security, but increases setup complexity and risk of locking myself out if I lose my SSH key. This is exactly the kind of security vs usability tension that operating systems have to manage in real environments.
