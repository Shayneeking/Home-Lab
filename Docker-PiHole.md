---

# 🛡️ **Install Pi-hole in Docker on Ubuntu 25.04**

This guide helps you set up **Pi-hole** (network-wide ad blocker) using **Docker** on **Ubuntu 25.04**, storing all files in `/var/opt/pihole`.

---

## ✅ Table of Contents

1. [Install Docker and Docker Compose](#1-install-docker-and-docker-compose)
2. [Create Pi-hole Docker Setup Directory](#2-create-pi-hole-docker-setup-directory)
3. [Create `docker-compose.yml` for Pi-hole](#3-create-docker-composeyml-for-pi-hole)
4. [Set File Permissions](#4-set-file-permissions)
5. [Launch Pi-hole](#5-launch-pi-hole)
6. [Access the Pi-hole Web Interface](#6-access-the-pi-hole-web-interface)
7. [Set Pi-hole as Your Network DNS](#7-set-pi-hole-as-your-network-dns)
8. [Common Docker Commands](#8-common-docker-commands)

---

## 1. 🐳 Install Docker and Docker Compose

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable --now docker
```

(Optional) Allow your user to run Docker:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## 2. 📁 Create Pi-hole Docker Setup Directory

```bash
sudo mkdir -p /var/opt/pihole
cd /var/opt/pihole
```

---

## 3. 📝 Create `docker-compose.yml` for Pi-hole

Create the file:

```bash
sudo vim /var/opt/pihole/docker-compose.yml
```

Press `i` to enter **Insert mode**, then paste the following (customize values as needed):

```yaml
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    environment:
      TZ: 'America/New_York'            # Change to your timezone
      WEBPASSWORD: 'secure_password'    # Change to a strong password
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

Once done, press `Esc`, then type:

```bash
:wq
```

…and hit `Enter` to save and exit.

---


## 4. 🔐 Set File Permissions

Create the volume directories:

```bash
sudo mkdir -p /var/opt/pihole/etc-pihole /var/opt/pihole/etc-dnsmasq.d
sudo chmod -R 755 /var/opt/pihole
```

---

## 5. 🚀 Launch Pi-hole

Navigate to the setup directory and start the container:

```bash
cd /var/opt/pihole
sudo docker-compose up -d
```

Check status:

```bash
sudo docker ps
```

---

## 6. 🌐 Access the Pi-hole Web Interface

Open in your browser:

```
http://<your-static-ip>/admin
```

Login using the password you set in the `docker-compose.yml` (`WEBPASSWORD`).

---

## 7. 📡 Set Pi-hole as Your Network DNS

* **Router-wide (recommended)**:
  Set your router’s primary DNS server to your Pi-hole static IP (e.g., `192.168.1.10`).

* **Per device**:
  Manually configure DNS settings on individual devices to use your Pi-hole IP.

---

## 8. 🔄 Common Docker Commands

| Action             | Command                              |
| ------------------ | ------------------------------------ |
| Restart Pi-hole    | `sudo docker restart pihole`         |
| Stop Pi-hole       | `sudo docker stop pihole`            |
| View logs          | `sudo docker logs -f pihole`         |
| Update image       | `sudo docker pull pihole/pihole`     |
| Recreate container | `sudo docker-compose up -d` (in dir) |

---
