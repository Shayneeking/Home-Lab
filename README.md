# 🔐 **Ubuntu 25.04 Hardening Guide**

## Table of Contents

1. [User and Authentication Hardening](#1-user-and-authentication-hardening)
2. [System Preparation](#2-system-preparation)

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
