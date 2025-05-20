Hardening Ubuntu 25.04 (or any Ubuntu system) means improving its security posture to reduce the risk of unauthorized access, data breaches, or service disruption. Ubuntu 25.04 is a recent release, so here’s a practical, up-to-date hardening checklist tailored for it:

---

## 🔒 Ubuntu 25.04 Hardening Guide

### ✅ 1. **Keep the System Updated**

Ensure security patches and updates are applied:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

---

### ✅ 2. **Minimal Install / Remove Unused Software**

Reduce the attack surface:

```bash
sudo apt autoremove --purge
```

Also review services:

```bash
systemctl list-units --type=service --state=running
```

Disable any unnecessary ones:

```bash
sudo systemctl disable service-name
```

---

### ✅ 3. **Create a Non-root User**

Avoid using `root` directly:

```bash
sudo adduser yourusername
sudo usermod -aG sudo yourusername
```

---

### ✅ 4. **Enable and Configure UFW (Uncomplicated Firewall)**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
```

Check status:

```bash
sudo ufw status
```

---

### ✅ 5. **Secure SSH Access**

Edit SSH config:

```bash
sudo nano /etc/ssh/sshd_config
```

Change or add:

```
PermitRootLogin no
PasswordAuthentication no
AllowUsers yourusername
```

Then restart:

```bash
sudo systemctl restart sshd
```

Optional:

* Use **key-based authentication** instead of passwords
* Change default port (e.g., `Port 2222`)

---

### ✅ 6. **Install Fail2Ban**

Protect against brute-force login attempts:

```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban --now
```

Create a local jail configuration:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

---

### ✅ 7. **Enable AppArmor (Ubuntu's Mandatory Access Control)**

AppArmor is enabled by default, verify:

```bash
sudo apparmor_status
```

Make sure key profiles are loaded (`/etc/apparmor.d/`).

---

### ✅ 8. **Install and Use Lynis for Security Auditing**

```bash
sudo apt install lynis
sudo lynis audit system
```

Lynis gives you recommendations for further hardening.

---

### ✅ 9. **Secure Shared Memory**

Edit `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

Add:

```
tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0
```

---

### ✅ 10. **Enable Automatic Security Updates**

Make sure `/etc/apt/apt.conf.d/50unattended-upgrades` exists and is configured:

```bash
sudo dpkg-reconfigure unattended-upgrades
```

---

### ✅ 11. **Use a Strong Password Policy**

Install `libpam-pwquality`:

```bash
sudo apt install libpam-pwquality
```

Then configure `/etc/security/pwquality.conf`:

```
minlen = 12
dcredit = -1
ucredit = -1
ocredit = -1
lcredit = -1
```

---

### ✅ 12. **Audit Logging**

Install auditd:

```bash
sudo apt install auditd
sudo systemctl enable auditd --now
```

---

## 🚨 Bonus: Advanced Options

* Set up **disk encryption** (LUKS)
* Enable **2FA** with `google-authenticator` or `pam_oath`
* Use **SELinux** (complex, optional as AppArmor is standard in Ubuntu)
* Consider **intrusion detection systems** (like OSSEC or AIDE)

---

Would you like a shell script that applies many of these automatically, or are you managing multiple systems (so we could talk Ansible/hardening automation)?
