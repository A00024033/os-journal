1. System Architecture Diagram
![System Architecture Diagram](images/diagram.png)
The system consists of two systems:
Client System: The host machine (Windows laptop)
Server System: Ubuntu Server 24.04.3 LTS running inside VirtualBox

The host machine is used as the administration and client device, while the Ubuntu 
Server virtual machine acts as the production server. Communication between the two 
systems occurs over a bridged network, which allows the server to appear as a real 
machine on the same local network.

Architecture Description

The host machine connects to the server using:
  -SSH (Port 22) for remote administration
  -HTTP (Port 80) for web access to the Apache server
The server VM runs in headless mode and provides:
  -Web services (Apache)
  -Secure remote access (SSH)
  -Firewall protection (UFW)
  -Intrusion prevention (Fail2Ban)
  -Mandatory access control (AppArmor)
This architecture reflects a real-world client–server deployment model and allows 
proper testing of security, networking, and services.

2. Distribution Selection & Justification

The selected server operating system for this project is:
  -Ubuntu Server 24.04.3 LTS (Noble)
This distribution was chosen after comparison with alternative server operating 
systems such as Debian Server and CentOS/Rocky Linux.

Justification for Choosing Ubuntu Server
  -Long-Term Support (LTS): Ubuntu Server 24.04 provides 5 years of security updates, 
making it suitable for stable production deployment.
  -Strong Community Support: Ubuntu has one of the largest Linux communities, ensuring 
extensive documentation, tutorials, and troubleshooting resources.
  -Excellent Package Management: The apt package manager is user-friendly, fast, and 
reliable.
  -Security Features Built-In: Native support for:
    +UFW firewall
    +AppArmor mandatory access control
    +Fail2Ban compatibility
  -Cloud and Enterprise Compatibility: Ubuntu is widely used in cloud platforms such as
AWS and Azure.

Comparison with Alternatives
| Distribution             | Strengths                                              | Limitations                                                        |
| ------------------------ | ------------------------------------------------------ | ------------------------------------------------------------------ |
|   Ubuntu Server          | LTS support, easy package management, strong community | Slightly higher resource usage than minimal distros                |
|   Debian Server          | Very stable, lightweight, secure                       | Older packages, slower updates                                     |
|   CentOS / Rocky Linux   | Enterprise-grade, stable                               | More complex administration, no direct support after CentOS Stream |


Ubuntu Server was chosen as the best balance between stability, security, ease of use,
and professional relevance.

3. Workstation Configuration Decision

The workstation used for this project is the host Windows laptop, rather than a second
Linux desktop virtual machine.

Justification
 The Windows host already provides:
  -A reliable SSH client
  -A web browser for accessing the Apache server
  -VirtualBox management tools
 Using the host avoids:
  -Unnecessary resource usage from an extra desktop VM
  -Additional network complexity
 This still fully satisfies the requirement of two systems:
  -Client system: Windows host
  -Server system: Ubuntu Server VM
This approach reflects a realistic administration scenario, where system administrators
often manage Linux servers remotely from Windows workstations.

4. Network Configuration (VirtualBox & IP Addressing)
 VirtualBox Network Settings
  Network Adapter Type: Bridged Adapter
  Physical Interface: Wi-Fi Network Card
  Purpose: To place the server directly on the same network as the host
 Server IP Address
The server was automatically assigned the following IPv4 address: 192.168.1.221
This allows:
  Direct SSH access from the host
  Direct browser access to Apache using HTTP
  Realistic firewall and intrusion testing
Active Services & Ports
| Service | Port | Purpose                      |
| ------- | ---- | ---------------------------- |
| SSH     | 22   | Secure remote administration |
| Apache  | 80   | Web server access            |

5. System Specifications Using CLI (Verification Evidence)
  System verification was completed using the required command-line tools.
Kernel & Architecture
uname -a :
 ![uname output](./images/sysinfo.png)
Confirms a 64-bit Linux kernel running on x86_64 architecture.

Memory Usage 
free -h :
![Memory Usage](images/memoryusage.png)
-Total RAM: ~1.9 GB
-Used: ~347 MB
-Swap: Disabled
  This confirms the system is lightweight and efficient.

Disk Usage : 
df -h :
![Disk Usage](images/diskusage.png)
Total Disk: 25 GB
Used: ~12%
Free: ~21 GB

Network Configuration
ip addr :
![IP address](images/ip.png)
Confirms the bridged interface and IPv4 address 192.168.1.221.

Operating System Verification
lsb_release -a : 
![OS Release](images/osrelease.png)
Confirms Ubuntu Server 24.04.3 LTS (Noble).

Reflection :
-This phase highlighted the importance of careful planning before system deployment.
Choosing an LTS server distribution ensured long-term stability and security. The use 
of bridged networking allowed the server to integrate seamlessly into the local network,
making security and web services more realistic.
-Working with a headless server reinforced professional system administration practices
and reduced the system’s attack surface. Using CLI tools to verify system specifications
strengthened confidence in validating real server environments.
