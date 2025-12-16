# Week 3: Application Selection for Performance Testing

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

  <a href="week4.html" style="
     background:#f4d7e3;
     padding:10px 18px;
     color:#5a3a45;
     border-radius:8px;
     text-decoration:none;
     border:1px solid #e7bdcc;
     font-weight:600;">
     ➡ Next: Week 4
  </a>

</div>

## Introduction

This phase focuses on selecting representative applications to evaluate system performance under different workload types. Before running any benchmarks or stress tests, it is essential to plan which applications will be used, what resources they are expected to consume, and how their behaviour will be monitored. This structured approach ensures that later performance testing is meaningful, controlled, and repeatable.

The applications selected in this phase will be installed and executed in later weeks to analyse CPU usage, memory consumption, disk I/O, and network performance on the Ubuntu Server.

---

## 1. Application Selection Matrix

To cover a broad range of real-world workloads, applications were selected to represent CPU-intensive, memory-intensive, I/O-intensive, network-intensive, and server-based workloads.

| Application              | Workload Type     | Justification                                                                                                       |
| ------------------------ | ----------------- | ------------------------------------------------------------------------------------------------------------------- |
| **stress-ng (CPU mode)** | CPU-intensive     | Generates controlled and repeatable CPU load, allowing analysis of processor utilisation and scheduling behaviour.  |
| **stress-ng (VM mode)**  | Memory-intensive  | Allocates and stresses system memory, useful for observing RAM usage and potential swap behaviour.                  |
| **fio**                  | I/O-intensive     | Industry-standard tool for benchmarking disk read/write performance and I/O latency.                                |
| **iperf3**               | Network-intensive | Measures network bandwidth and throughput, suitable for testing network performance between workstation and server. |
| **Apache2**              | Server workload   | Represents a realistic web server scenario, allowing evaluation of system behaviour under client request load.      |

These applications were chosen because they are lightweight, well-documented, and commonly used in both academic and industry environments.

---

## 2. Installation Documentation (Planned)

All applications will be installed on the Ubuntu Server using SSH from the Debian workstation, in line with the administrative constraint of remote management only.

The planned installation commands are:

```bash
sudo apt update
sudo apt install stress-ng fio iperf3 apache2 -y
```

These commands ensure that all required testing tools are installed using the official Ubuntu package repositories, maintaining system integrity and ease of updates.

---

## 3. Expected Resource Profiles

Before execution, it is important to define expected behaviour for each workload. This allows actual results to be compared against predictions in later analysis.

### stress-ng (CPU-intensive)

Expected to drive CPU utilisation close to 100% across available cores, with minimal impact on disk and network activity.

### stress-ng (Memory-intensive)

Expected to consume a large portion of available RAM and potentially trigger swap usage if memory limits are reached.

### fio (I/O-intensive)

Expected to generate high disk read/write activity, increased I/O wait times, and noticeable disk throughput changes.

### iperf3 (Network-intensive)

Expected to significantly increase network bandwidth usage while having minimal impact on disk usage and moderate CPU utilisation.

### Apache2 (Server workload)

Expected to show moderate CPU and memory usage under light load, increasing proportionally with concurrent client requests.

---

## 4. Monitoring Strategy

To measure system performance accurately, different monitoring tools will be used depending on the workload being tested.

| Application Type   | Monitoring Tools          | Purpose                                                                    |
| ------------------ | ------------------------- | -------------------------------------------------------------------------- |
| CPU workloads      | `top`, `htop`, `uptime`   | Monitor CPU usage, load averages, and process scheduling behaviour.        |
| Memory workloads   | `free -h`, `vmstat`       | Observe memory allocation, availability, and swap activity.                |
| Disk I/O workloads | `iostat`, `vmstat`        | Measure disk throughput, latency, and I/O wait times.                      |
| Network workloads  | `iperf3`, `ss`, `iftop`   | Monitor network bandwidth, connections, and traffic flow.                  |
| Server workloads   | `top`, Apache access logs | Evaluate server responsiveness and resource consumption under client load. |

This monitoring strategy ensures that each application is measured using appropriate tools, providing accurate and relevant performance data.

---

## Reflection

Planning application selection before executing performance tests helps prevent misleading results and uncontrolled testing. By defining expected resource profiles and monitoring strategies in advance, it becomes easier to identify abnormal behaviour, bottlenecks, or performance limitations when tests are later executed.

This phase highlights the importance of separating design from implementation, ensuring that performance testing in later weeks is structured, repeatable, and aligned with real-world system administration practices.

---

Whenever you’re ready, send **Week 4**.
You’re doing this properly.

