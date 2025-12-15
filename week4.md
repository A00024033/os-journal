Week 4 : Initial System Configuration & Security Implementation

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

  <a href="week5.html" style="
     background:#f4d7e3;
     padding:10px 18px;
     color:#5a3a45;
     border-radius:8px;
     text-decoration:none;
     border:1px solid #e7bdcc;
     font-weight:600;">
     ➡ Next: Week 5
  </a>

</div>

1. Creating a Non-Root Administrative User

To improve system security, I created a new non-root user called adminuser. This follows the principle of least privilege, meaning administrative tasks are not performed directly using the root account.

I used the adduser command to create the user and then added the user to the sudo group so that administrative commands can be executed only when required.

Commands used:

sudo adduser adminuser
sudo usermod -aG sudo adminuser
groups adminuser

[adminuser](adminuser.png)
[groupsadmin](groupsadm.png)

This confirms that adminuser has sudo privileges without being root.

2. SSH Key-Based Authentication Configuration

To secure remote access, SSH key-based authentication was configured instead of password authentication.

First, an SSH key pair was generated on the workstation. The public key was then copied to the server using ssh-copy-id, allowing passwordless authentication.

Commands used on the workstation:

ssh-keygen
ssh-copy-id eva@192.168.1.221

[ssh-keygen](keygen.png)
[ssh-copy-id](copyid.png)

After this, I was able to log in via SSH without entering a password, confirming that key-based authentication was working correctly.

3. SSH Server Hardening

The SSH configuration file was modified to improve security by disabling password authentication and root login, while ensuring that only authorised users can connect.

The following settings were applied in /etc/ssh/sshd_config:

PubkeyAuthentication yes

PasswordAuthentication no

PermitRootLogin no

AllowUsers eva adminuser

These changes ensure that:

Only SSH keys are accepted

Root login is disabled

Only approved users can access the server

After editing the file, the SSH service was restarted.

Command used:

sudo systemctl restart ssh

[sshd_config](sshdconfig.png)

This screenshot is showing the security settings.

4. Firewall Configuration (UFW)

A firewall was configured using UFW to restrict SSH access to only one specific workstation IP address. This significantly reduces the attack surface of the server.

First, existing rules were reset, then SSH access was allowed only from the workstation IP 192.168.1.183.

Commands used:

sudo ufw reset
sudo ufw allow from 192.168.1.183 to any port 22
sudo ufw enable
sudo ufw status numbered

[ufw](ufw.png)

The firewall rules confirm that SSH access is restricted to a single trusted IP address.

5. File Permission Verification for SSH

Correct file permissions were applied to ensure SSH security. The .ssh directory and authorized_keys file for adminuser were restricted to prevent unauthorised access.

Commands used:

sudo ls -ld /home/adminuser /home/adminuser/.ssh
sudo ls -l /home/adminuser/.ssh/authorized_keys


The output confirms:

.ssh directory has 700 permissions

authorized_keys file has 600 permissions

[ls](lspro.png)

6. SSH Access Evidence

Successful SSH access was tested using the new administrative user. The login confirms that SSH key-based authentication is functioning correctly and that root login is not required.

Command used:

ssh adminuser@192.168.1.221

[admin](adminworkst.png)

7. Remote Administration Evidence (via SSH)

To demonstrate remote administration, system and firewall commands were executed while logged in via SSH as adminuser.

Commands used:

whoami
sudo ufw status
uptime


The output confirms:

The session is running as adminuser

Administrative commands are executed remotely using sudo

The system is operational and monitored remotely

[admin_commands](admincom.png)

Conclusion

This phase successfully secured the server by implementing key-based SSH authentication, restricting firewall access to a single trusted workstation, and using a non-root administrative user with controlled privileges. These configurations improved the system’s security and ensured safe remote administration following best practices.
