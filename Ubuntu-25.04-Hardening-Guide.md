---

# 🔐 **Ubuntu 25.04 Hardening Guide**

A minimal hardening checklist to secure a fresh Ubuntu 25.04 server install for homelab or production use.

---

### 📋 Table of Contents

1. [User and Authentication Hardening](#1-user-and-authentication-hardening)
2. [Disable Root SSH Login](#2-disable-root-ssh-login)
3. [System Preparation](#3-system-preparation)
4. [Set a Static IP for the Host](#4-set-a-static-ip-for-the-host)

---

## 1. User and Authentication Hardening

If you **did not create a non-root user during installation**, begin by creating a secure administrative user.

### ❯ 1.1 Create an Admin User (Example: `sysadmin`)

```bash
adduser sysadmin
usermod -aG sudo sysadmin
```

**Explanation:**

* `adduser sysadmin` — Creates the user and prompts for a password and user info.
* `usermod -aG sudo sysadmin` — Adds the user to the `sudo` group.

Then switch to the new user:

```bash
su - sysadmin
```

All further steps should be completed as this non-root user.

---

### ❯ 1.2 Check if OpenSSH Server is Installed

Ensure SSH is installed for remote access:

```bash
dpkg -l | grep openssh-server
```

If not installed, run:

```bash
sudo apt update
sudo apt install openssh-server
```

---

## 2. Disable Root SSH Login

Restricting root SSH access reduces risk from brute-force attacks.

### ❯ 2.1 Edit `sshd_config`

```bash
sudo vim /etc/ssh/sshd_config
```

Set the following directive:

```
PermitRootLogin no
```

Apply changes:

```bash
sudo systemctl restart ssh
```

---

## 3. System Preparation

If you **did not configure the hostname during the installer**, set it manually now.

### ❯ 3.1 Change the Hostname

Edit the hostname file:

```bash
sudo vim /etc/hostname
```

Replace the current value (e.g., `ubuntu`) with your preferred name:

```
pi1
```

Then edit `/etc/hosts`:

```bash
sudo vim /etc/hosts
```

Set:

```
127.0.1.1    pi1
```

Apply and reboot:

```bash
sudo hostnamectl set-hostname pi1
sudo reboot
```

---

### ❯ 3.2 Update the System

Bring all packages up to date and enable automatic updates:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install unattended-upgrades
```

---

### ❯ 3.3 Enable Time Synchronization

Ensure system clock accuracy:

```bash
sudo timedatectl set-ntp true
```

---

## 4. 📌 Set a Static IP for the Host

Pi-hole and other services require a reliable, unchanging IP address.

### ❯ 4.1 Configure via Netplan

1. Identify the primary interface:

```bash
ip link
```

Assuming `eth0`, edit the Netplan config:

```bash
sudo vim /etc/netplan/50-cloud-init.yaml
```

Example configuration:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.10/24
      nameservers:
        addresses:
          - 1.1.1.1
          - 1.0.0.1
      routes:
        - to: default
          via: 192.168.1.1
```

Apply the changes:

```bash
sudo netplan apply
```

---
