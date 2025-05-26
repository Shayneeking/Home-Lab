---

# 🛡️ **Install Pi-hole in Docker on Ubuntu 25.04**

This guide helps you set up **Pi-hole** (network-wide ad blocker) using **Docker** on **Ubuntu 25.04**, with persistent data stored in `/var/opt/pihole`.

---

## ✅ Table of Contents

1. [Disable systemd-resolved (Free Port 53)](#1-disable-systemd-resolved-free-port-53)
2. [Install Docker and Docker Compose](#2-install-docker-and-docker-compose)
3. [Create Pi-hole Docker Setup Directory](#3-create-pi-hole-docker-setup-directory)
4. [Create `docker-compose.yml` for Pi-hole](#4-create-docker-composeyml-for-pi-hole)
5. [Set File Permissions](#5-set-file-permissions)
6. [Launch Pi-hole](#6-launch-pi-hole)
7. [Set Pi-hole Admin Password](#7-set-pi-hole-admin-password)
8. [Access the Pi-hole Web Interface](#8-access-the-pi-hole-web-interface)
9. [Set Pi-hole as Your Network DNS](#9-set-pi-hole-as-your-network-dns)
10. [Common Docker Commands](#10-common-docker-commands)

---

## 1. ❌ Disable systemd-resolved (Free Port 53)

Pi-hole needs to bind to port `53`, but `systemd-resolved` often occupies it on Ubuntu.

Check what's using port 53:

```bash
sudo lsof -i :53
```

If `systemd-resolved` is listed, disable and stop it:

```bash
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
```

Replace the existing `/etc/resolv.conf`:

```bash
sudo rm /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf
```

---

## 2. 🐳 Install Docker and Docker Compose

Install Docker packages and start the service:

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable --now docker
```

(Optional) Add your user to the Docker group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## 3. 📁 Create Pi-hole Docker Setup Directory

```bash
sudo mkdir -p /var/opt/pihole
cd /var/opt/pihole
```

---

## 4. 📝 Create `docker-compose.yml` for Pi-hole

Edit the Docker Compose file using `vim`:

```bash
sudo vim /var/opt/pihole/docker-compose.yml
```

Press `i` to enter Insert mode and paste the following:

```yaml
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    environment:
      TZ: 'America/New_York'            # Change to your timezone
    volumes:
      - '/var/opt/pihole/etc-pihole:/etc/pihole'
      - '/var/opt/pihole/etc-dnsmasq.d:/etc/dnsmasq.d'
    dns:
      - 127.0.0.1
      - 1.1.1.1
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
```

Then press `Esc`, type `:wq`, and press `Enter` to save and exit.

---

## 5. 🔐 Set File Permissions

Create the necessary volume directories:

```bash
sudo mkdir -p /var/opt/pihole/etc-pihole /var/opt/pihole/etc-dnsmasq.d
sudo chmod -R 755 /var/opt/pihole
```

---

## 6. 🚀 Launch Pi-hole

Start the Pi-hole container:

```bash
cd /var/opt/pihole
sudo docker-compose up -d
```

Verify it’s running:

```bash
sudo docker ps
```

---

## 7. 🔑 Set Pi-hole Admin Password

Set the password for the web interface:

```bash
sudo docker exec -it pihole pihole -a setpassword
```

You’ll be prompted to enter and confirm a new password.
To clear the password and disable login, just press `Enter` twice.

---

## 8. 🌐 Access the Pi-hole Web Interface

Open your browser and navigate to:

```
http://<your-static-ip>/admin
```

Login using the password you just set in step 7.

---

## 9. 📡 Set Pi-hole as Your Network DNS

* **Router-wide (recommended)**:
  Set your router’s DNS to Pi-hole’s static IP (e.g., `192.168.1.10`).

* **Per device**:
  Manually set your device DNS to point to the Pi-hole IP.

---

## 10. 🔄 Common Docker Commands

| Action             | Command                              |
| ------------------ | ------------------------------------ |
| Restart Pi-hole    | `sudo docker restart pihole`         |
| Stop Pi-hole       | `sudo docker stop pihole`            |
| View logs          | `sudo docker logs -f pihole`         |
| Update image       | `sudo docker pull pihole/pihole`     |
| Recreate container | `sudo docker-compose up -d` (in dir) |

---
