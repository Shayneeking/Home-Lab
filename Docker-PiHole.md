---

# 🛡️ **Install Pi-hole in Docker on Ubuntu 25.04**

This guide helps you set up **Pi-hole** (network-wide ad blocker) using **Docker** on **Ubuntu 25.04**.

---

## ✅ Table of Contents

2. [Install Docker and Docker Compose](#2-install-docker-and-docker-compose)
3. [Create Pi-hole Docker Setup Directory](#3-create-pi-hole-docker-setup-directory)
4. [Create `docker-compose.yml` for Pi-hole](#4-create-docker-composeyml-for-pi-hole)
5. [Set File Permissions](#5-set-file-permissions)
6. [Launch Pi-hole](#6-launch-pi-hole)
7. [Access the Pi-hole Web Interface](#7-access-the-pi-hole-web-interface)
8. [Set Pi-hole as Your Network DNS](#8-set-pi-hole-as-your-network-dns)
9. [Common Docker Commands](#9-common-docker-commands)

---

## 2. 🐳 Install Docker and Docker Compose

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

## 3. 📁 Create Pi-hole Docker Setup Directory

```bash
mkdir -p ~/pihole
cd ~/pihole
```

---

## 4. 📝 Create `docker-compose.yml` for Pi-hole

Create the file:

```bash
nano docker-compose.yml
```

Paste the following (customize values as needed):

```yaml
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    environment:
      TZ: 'Europe/London'            # Change to your timezone
      WEBPASSWORD: 'secure_password' # Change to a strong password
    volumes:
      - './etc-pihole/:/etc/pihole/'
      - './etc-dnsmasq.d/:/etc/dnsmasq.d/'
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

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

---

## 5. 🔐 Set File Permissions

Create the volume directories and make sure they’re accessible:

```bash
mkdir -p etc-pihole etc-dnsmasq.d
chmod -R 755 .
```

---

## 6. 🚀 Launch Pi-hole

Start the container:

```bash
docker-compose up -d
```

Check status:

```bash
docker ps
```

---

## 7. 🌐 Access the Pi-hole Web Interface

Open in your browser:

```
http://<your-static-ip>/admin
```

Login using the password you set in the `docker-compose.yml` (`WEBPASSWORD`).

---

## 8. 📡 Set Pi-hole as Your Network DNS

* **Router-wide (recommended)**:
  Set your router’s primary DNS server to your Pi-hole static IP (e.g., `192.168.1.10`).

* **Per device**:
  Manually configure DNS settings on individual devices to use your Pi-hole IP.

---

## 9. 🔄 Common Docker Commands

| Action             | Command                     |
| ------------------ | --------------------------- |
| Restart Pi-hole    | `docker restart pihole`     |
| Stop Pi-hole       | `docker stop pihole`        |
| View logs          | `docker logs -f pihole`     |
| Update image       | `docker pull pihole/pihole` |
| Recreate container | `docker-compose up -d`      |

---

## ✅ You're Done!

You now have **Pi-hole running in Docker** on a **Ubuntu host with a static IP**. Enjoy network-wide ad blocking.

---

Let me know if you'd like:

* Pi-hole + Unbound setup (for DNS privacy)
* IPv6 or Docker network tweaks
* Auto-renewing SSL via Nginx or Caddy

I can customize it for your use case!
