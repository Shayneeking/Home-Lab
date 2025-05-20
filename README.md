# 🔐 **Ubuntu 25.04 Hardening Guide**

## Table of Contents

1. [User and Authentication Hardening](#1-user-and-authentication-hardening)
2. [System Preparation](#2-system-preparation)
3. [Service and Application Hardening](#4-service-and-application-hardening)
4. [Kernel and System Hardening](#5-kernel-and-system-hardening)
5. [Logging and Auditing](#6-logging-and-auditing)
6. [File System Hardening](#7-file-system-hardening)
7. [Updates and Patch Management](#8-updates-and-patch-management)
8. [Security Tools](#9-security-tools)
9. [Final Notes](#10-final-notes)

---

## 1. User and Authentication Hardening

### ❯ **Create an Admin User (Example: `sysadmin`)**

Use a strong username that isn't easy to guess. Here's how to create a user called `sysadmin` and give them sudo privileges:

```bash
adduser sysadmin
usermod -aG sudo sysadmin
```

**Explanation:**

* `adduser sysadmin` — Creates the user and prompts you to set a password and basic user info.
* `usermod -aG sudo sysadmin` — Adds `sysadmin` to the `sudo` group so they can perform admin tasks.

After creating the user, switch to it:

```bash
su - sysadmin
```

Now you can proceed with the rest of the hardening process from this non-root, sudo-enabled account.

---

### ❯ **Disable Root SSH Login**

Edit `/etc/ssh/sshd_config`:

```plaintext
PermitRootLogin no
```

Then:

```bash
sudo systemctl restart ssh
```

---

## 2. System Preparation

### ❯ **Update the System**

Keep the system up-to-date:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install unattended-upgrades
```

---

### ❯ **Configure Time Synchronization**

Ensure the system time is synchronized:

```bash
sudo timedatectl set-ntp true
```

---

## 3. Service and Application Hardening

### ❯ **Secure SSH**

Edit `/etc/ssh/sshd_config` to secure SSH access:

```plaintext
Port 2222
Protocol 2
PermitEmptyPasswords no
PasswordAuthentication no  # if using SSH keys
PermitRootLogin no
```

Then restart SSH:

```bash
sudo systemctl restart ssh
```

---

### ❯ **Install Fail2Ban**

Fail2Ban helps protect against brute-force attacks. Install it:

```bash
sudo apt install fail2ban
```

Configure Fail2Ban by editing `/etc/fail2ban/jail.local`:

```ini
[sshd]
enabled = true
port = 2222
logpath = %(sshd_log)s
maxretry = 4
```

Restart Fail2Ban:

```bash
sudo systemctl restart fail2ban
```

---

## 4. Kernel and System Hardening

### ❯ **Enable AppArmor**

AppArmor enhances security by restricting application capabilities:

```bash
sudo apt install apparmor apparmor-profiles apparmor-utils
sudo aa-enforce /etc/apparmor.d/*
```

---

### ❯ **Disable Core Dumps**

Prevent core dumps to protect sensitive data:

Edit `/etc/security/limits.conf`:

```plaintext
* hard core 0
```

---

### ❯ **Harden sysctl Configuration**

Edit `/etc/sysctl.d/99-sysctl.conf`:

```plaintext
# IPv4
net.ipv4.ip_forward = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.tcp_syncookies = 1

# IPv6
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
```

Apply changes:

```bash
sudo sysctl -p
```

---

## 5. Logging and Auditing

### ❯ **Use Systemd Journal**

View logs with `journalctl`:

```bash
journalctl -xe
```

### ❯ **Install and Configure Auditd**

Install the auditing tools:

```bash
sudo apt install auditd audispd-plugins
sudo systemctl enable auditd
```

Example rule in `/etc/audit/rules.d/audit.rules`:

```plaintext
-w /etc/passwd -p wa -k passwd_changes
-w /etc/shadow -p wa -k shadow_changes
```

---

## 6. File System Hardening

### ❯ **Configure Secure Mount Options**

Edit `/etc/fstab` to harden file system security:

```plaintext
tmpfs /tmp tmpfs defaults,noexec,nosuid,nodev 0 0
```

### ❯ **Restrict Permissions**

Limit file access by changing permissions:

```bash
sudo chmod 700 /root
sudo chmod -R o-rwx /home/*
```

---

## 7. Updates and Patch Management

### ❯ **Configure Unattended Upgrades**

Enable automatic security updates:

Edit `/etc/apt/apt.conf.d/50unattended-upgrades`:

```plaintext
Unattended-Upgrade::Allowed-Origins {
    "Ubuntu stable";
    "Ubuntu 25.04-security";
};
Unattended-Upgrade::Automatic-Reboot "true";
```

---

## 8. Security Tools

Install security tools to enhance monitoring and protection:

```bash
sudo apt install chkrootkit rkhunter clamav lynis nmap
```

| Tool       | Purpose           |
| ---------- | ----------------- |
| chkrootkit | Rootkit scanner   |
| rkhunter   | Rootkit scanner   |
| clamav     | Antivirus         |
| lynis      | Security auditing |
| nmap       | Network scanning  |

---

## 9. Final Notes

* **Regularly Review Logs:** Use `journalctl` or check `/var/log/` for any unusual activity.
* **Disable USB (Optional):** If not needed, disable USB storage:

```bash
echo "blacklist usb-storage" | sudo tee /etc/modprobe.d/disable-usb.conf
```

* **Audit Periodically:** Run system audits using tools like `lynis`:

```bash
sudo lynis audit system
```

---

### Additional Notes:

* Always remember to test changes in a safe environment before applying them to a production system.
* Regularly check for updates to both Ubuntu and any security tools installed.

---

