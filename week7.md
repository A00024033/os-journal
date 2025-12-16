# 🔐 Week 7 – Security Audit and System Evaluation

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
</div>

## Phase Overview

This phase focused on conducting a comprehensive security audit to evaluate the overall security posture of the deployed Ubuntu Server. The audit assessed infrastructure hardening, network exposure, SSH access control, running services, and residual risks. The objective was to verify that the system complies with security best practices and builds upon the controls implemented in previous phases.

All tasks were carried out via secure SSH access using a non-root administrative user, in line with coursework constraints.

---

## Audit Methodology

The security audit was conducted using a layered approach:

* Host-based security assessment using Lynis
* Network security assessment using Nmap
* SSH configuration and access control verification
* Review and justification of running services
* Network listening port analysis
* Identification of remaining security risks

Evidence was captured at each stage through command execution outputs.

---

## Infrastructure Security Audit (Lynis)

Lynis was used to perform a full system security audit and evaluate the server’s hardening level.

The audit was executed using the following command:

```bash
sudo lynis audit system
```

The audit summary reported the following:

```text
Hardening index : 64
Tests performed : 262
Firewall        : Enabled
Malware scanner : Not installed
```

The system achieved a hardening index of **64**, indicating a solid security baseline. The firewall was correctly detected as active. Lynis highlighted the absence of a malware scanner, which was documented as a potential improvement rather than a critical vulnerability. No severe security misconfigurations were identified.

📸 **Screenshot placement:**

* Lynis audit summary and hardening index

---

## Network Security Assessment (Nmap)

To evaluate network exposure and identify open ports, Nmap was executed from the workstation against the server.

A SYN (stealth) scan was performed using:

```bash
sudo nmap -sS -Pn 192.168.1.221
```

The scan returned the following result:

```text
PORT   STATE SERVICE
22/tcp open  ssh
Not shown: 999 filtered tcp ports
```

A service version detection scan was then executed:

```bash
sudo nmap -sV 192.168.1.221
```

This confirmed:

```text
22/tcp open ssh OpenSSH 9.6p1 Ubuntu
```

The results confirm that only SSH is exposed externally. All other ports are filtered by the firewall. The SSH service is running a modern and supported version, reducing exposure to known vulnerabilities.

📸 **Screenshot placement:**

* Nmap SYN scan results
* Nmap service/version detection

---

## SSH Security Verification

The SSH daemon configuration was verified to ensure secure authentication and access controls.

The following command was used:

```bash
sudo sshd -T | grep -E "permitrootlogin|passwordauthentication|pubkeyauthentication|allowusers"
```

The output confirmed:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers eva adminuser
```

This configuration enforces strong SSH security by disabling root login, preventing password-based authentication, enforcing public key authentication, and restricting access to authorised users only.

📸 **Screenshot placement:**

* SSH security verification output

---

## Service Inventory and Justification

All active services were reviewed to ensure that only necessary services are running.

The following command was used:

```bash
systemctl list-units --type=service --state=running
```

Key services identified include SSH for remote administration, Fail2Ban for intrusion prevention, system logging services, unattended upgrades for automatic security updates, and essential system and network management services. No unnecessary or insecure services were identified.

📸 **Screenshot placement:**

* Running services list

---

## Network Listening Ports Review

Listening ports were reviewed to confirm minimal network exposure.

The following command was executed:

```bash
ss -tulnp
```

The output confirmed that SSH is the only service listening on a public-facing interface. All other services are bound to localhost or internal interfaces, reducing the system’s attack surface.

📸 **Screenshot placement:**

* Listening ports output (`ss -tulnp`)

---

## Remaining Risk Assessment

The audit identified the following residual risks:

* No malware scanning software installed
* SSH remains an exposed service, although strongly secured
* No host-based intrusion detection system beyond Fail2Ban

These risks are considered acceptable within the scope of the deployment and are documented for potential future hardening.

---

## Overall System Evaluation

The security audit confirms that the system is properly hardened at both the operating system and network levels. Access controls are correctly enforced, network exposure is minimal, and all running services are justified. The system fully complies with the security objectives of the coursework.

---

## Conclusion

This phase validated the effectiveness of the security controls implemented throughout earlier stages. The server demonstrates a strong security posture, minimal attack surface, and adherence to best practices, providing a secure foundation for ongoing operation and monitoring.

