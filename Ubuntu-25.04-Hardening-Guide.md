# 🔐 **Ubuntu 25.04 Hardening Guide**

## Table of Contents

1. [User and Authentication Hardening](#1-user-and-authentication-hardening)
2. [System Preparation](#2-system-preparation)
3. [Set a Static IP for the Host](#3-set-a-static-ip-for-the-host)

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

Edit the sshd config file

```bash
sudo vim /etc/ssh/sshd_config
```

Add the following

```plaintext
PermitRootLogin no
```

Then:

```bash
sudo systemctl restart ssh
```

---

## 2. System Preparation

### ❯ 🖥️ Change the Hostname

Changing the hostname makes your Pi-hole server easier to identify on the network.

### To change it:

1. Edit the hostname file:

```bash
sudo vim /etc/hostname
```

Replace the existing name (e.g., `ubuntu`) with your preferred name, such as:

```
pi1
```

2. Update `/etc/hosts` to match:

```bash
sudo vim /etc/hosts
```

Delete everything in the file and add

```
127.0.1.1    pi1
```

3. Apply the new hostname:

```bash
sudo hostnamectl set-hostname pi1
```

4. Reboot for changes to fully take effect:

```bash
sudo reboot
```

---

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

## 3. 📌 Set a Static IP for the Host

Pi-hole must run on a **static IP** to ensure other devices can always reach it for DNS.

### ❯ **Set static IP via Netplan**

Identify your network interface:

```bash
ip link
```

Assume your interface is `etho`. Edit the config:

```bash
sudo vim /etc/netplan/50-cloud-init.yaml
```

Example static IP config:

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

Apply:

```bash
sudo netplan apply
```
