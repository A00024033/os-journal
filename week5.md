# Week 5: Advanced Security and Monitoring Infrastructure

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

  <a href="week6.html" style="
     background:#f4d7e3;
     padding:10px 18px;
     color:#5a3a45;
     border-radius:8px;
     text-decoration:none;
     border:1px solid #e7bdcc;
     font-weight:600;">
     ➡ Next: Week 6
  </a>

</div>

---

## 1. Access Control Implementation Using AppArmor

In this phase, mandatory access control was implemented using **AppArmor**, which is the default access control framework on Ubuntu systems. AppArmor enhances system security by enforcing predefined security profiles that restrict what applications can access and execute.

The status of AppArmor was verified to confirm that the service was active and that security profiles were loaded and enforced. Additional checks were performed to demonstrate how access control settings can be tracked and reported.

### Commands used:

```bash
sudo systemctl status apparmor
sudo aa-status
sudo aa-status --verbose
```

These outputs confirm that AppArmor is enabled, profiles are loaded, and enforcement is active, ensuring that applications operate within defined security boundaries.

![status\_apparmor](images/apparmor.png)
![aa-status](images/aastatus.png)
![verbose](images/verbose.png)

---

## 2. Automatic Security Updates Configuration

Automatic security updates were configured to ensure that critical patches are applied without manual intervention. This reduces the risk of vulnerabilities caused by outdated software and improves long-term system security.

The `unattended-upgrades` package was installed and enabled, allowing the system to automatically install important security updates.

### Commands used:

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
cat /etc/apt/apt.conf.d/20auto-upgrades
```

![20auto-upgrades](images/20auto.png)

The configuration file confirms that automatic package list updates and unattended upgrades are enabled.

---

## 3. Intrusion Detection Using Fail2Ban

Fail2Ban was installed and configured to provide intrusion detection and automated protection against brute-force attacks. It monitors authentication logs and temporarily bans IP addresses that exhibit malicious behaviour, such as repeated failed SSH login attempts.

The service was verified to be running, and the SSH jail was confirmed to be active, ensuring protection for remote access services.

### Commands used:

```bash
sudo systemctl start fail2ban
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

![fail2ban-status](images/fail2ban.png)
![fail2ban-status-sshd](images/fail2bansshd.png)

---

## 4. Security Baseline Verification Script

A security baseline verification script named **security-baseline.sh** was created to verify all security configurations implemented during Phases 4 and 5. The script checks SSH hardening settings, firewall rules, AppArmor status, automatic security updates, and Fail2Ban intrusion detection.

The script was fully commented to explain each check and was executed remotely via SSH to validate the server’s security posture.

### Script execution:

```bash
bash security-baseline.sh
```

![ls](images/lsssh.png)
![bash](images/bashssh.png)

The output confirms that all security controls are correctly configured and operational.

---

## 5. Remote Monitoring Script

A remote monitoring script named **monitor-server.sh** was created on the workstation to collect performance metrics from the server via SSH. The script retrieves system uptime, CPU load, memory usage, and disk usage, providing a clear overview of the server’s operational status.

This approach allows for efficient remote monitoring without requiring direct access to the server console.

### Script execution:

```bash
./monitor-server.sh
```

![monitor-server](images/lsmonitor.png)

---

## Conclusion

This phase successfully enhanced the server’s security by implementing mandatory access control, automated security updates, intrusion detection, and both verification and monitoring scripts. Together, these measures provide a robust security and monitoring framework that supports safe remote administration and ongoing system reliability.
