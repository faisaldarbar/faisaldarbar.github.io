---
title: "📌 Unified Full-Stack Infrastructure & DevOps Blueprint"
weight: 1
date: 2025-08-03T02:30:00Z
description: "Scope: This document outlines the setup, architecture, service layout, VM roles, storage division, backup plan, and DevOps tools for a modern self-hosted app development and deployment environment, based on Docker, Linux VMs, and modern JS full-stack tools (React, Next.js, Express, etc.)."
categories: ["Dev Docs"]
tags: [full-stack, devops, infrastructure, react, next.js, node.js, express, docker, docker-compose, coolify, postgres, redis, self-hosted, cloudflare-tunnel, react-native, expo, proxmox, pfSense, rclone, ubuntu-server, microservices, digital-products, solo-developer, backend, frontend, vm-architecture, developer-tools]

cover:
  image: "/images/unified-full-stack-infrastructure-and-devops-blueprint.webp"
  alt: "Unified Full-Stack Infrastructure & DevOps Blueprint"
  caption: "AI Generated Photo by ChatGPT"
---

## 1. 🎯 Goals & Objectives

* Host full-stack web apps (React + Next.js + Node.js/Express + PostgreSQL)
* Add Android-first cross-platform apps via React Native + Expo
* Build, test, and deploy apps independently (no external cloud lock-in)
* Centralize Docker-based backend on a self-managed VM
* Use Coolify separately for selected apps or GUI deploys
* Store and backup media assets (e.g., YouTube videos, thumbnails)
* Avoid PaaS bills (like Vercel/Supabase) and overcome CGNAT
* Use rclone for scheduled offsite backups to OneDrive

---

## 2. 🧱 Virtual Machine Layout (Proxmox)

### 2.1 `dev-ubuntu` VM (Core App Backend & Services)

| Attribute    | Value                                       |
| ------------ | ------------------------------------------- |
| OS           | Ubuntu Server (LTS)                         |
| vCPU         | 6 cores                                     |
| RAM          | 8–10 GB                                     |
| Disk         | 350 GB (NVMe SSD)                           |
| Network      | VMLAN (10.0.1.x)                            |
| Purpose      | Full-stack app backend, Docker Compose, DBs |
| DNS Hostname | Use real domains/subdomains                 |

### Disk Breakdown for `dev-ubuntu`:

```
/ (root)               → 50 GB      # OS, Docker, binaries
/data/
├── docker/            → 20 GB      # Docker volumes & overlay
├── postgres/          → 25 GB      # Persistent DB storage
├── redis/             → 5 GB       # Redis volume (optional persistence)
├── backups/           → 50 GB      # Compressed backups, DB dumps
```

> Total usage ≈ **150 GB** (room for more services/apps)

---

### 2.2 `coolify-ubuntu` VM (Isolated PaaS Deployment)

| Attribute    | Value                                      |
| ------------ | ------------------------------------------ |
| OS           | Ubuntu Server (LTS)                        |
| vCPU         | 2–4 cores                                  |
| RAM          | 4 GB                                       |
| Disk         | 50 GB                                      |
| Network      | VMLAN (10.0.1.x)                           |
| Purpose      | GUI deploy PaaS, optional frontend deploys |
| DNS Hostname | Use real domains/subdomains                |

> Will run as **root**, self-contained. It will not deploy containers to `dev-ubuntu`.

---

### 2.3 `pbs` VM (Proxmox Backup Server)

* Already provisioned.
* Will store regular VM snapshots.
* Use `rclone` within VMs for application/data-level backup to OneDrive.

---

## 3. 🖥️ Bare Metal FD-HUAWEI Laptop (Storage Node)

| Role   | Media storage, NFS/Samba host |
| ------ | ----------------------------- |
| OS     | Ubuntu Server (headless)      |
| RAM    | 8 GB                          |
| Disk   | 240 GB SSD (full usable)      |
| Access | SSH + Samba (LAN)             |

### Mount Plan:

* Mount `/mnt/digital_assets/` on HP laptop or edit directly via LAN.
* No need to mount on `dev-ubuntu` unless media assets are used in app (optional SSHFS).

---

## 4. ⚙️ Services & Tools Stack

### 4.1 Inside `dev-ubuntu` (via Docker Compose)

| Service      | Image / Stack         | Description                               |
| ------------ | --------------------- | ----------------------------------------- |
| `frontend`   | Next.js (Node-based)  | React + SSR frontend app                  |
| `api-server` | Node.js + Express     | Backend API server                        |
| `postgres`   | `postgres:16`         | Relational DB, stored in `/data/postgres` |
| `redis`      | `redis:alpine`        | Cache/session store                       |
| `nginx`      | Optional (static app) | Only if static build is needed            |

> Docker Compose manages build, startup, networking, volumes.

### 4.2 On FD-HP Laptop

* React Native + Expo for Android apps
* Connects to `dev-ubuntu` backend via Cloudflare Tunnel or VPN

---

## 5. 🔒 Security & Networking

| Feature           | Status / Plan                                          |
| ----------------- | ------------------------------------------------------ |
| VLAN Isolation    | `dev-ubuntu` and `coolify-ubuntu` on 10.0.1.0/24       |
| pfSense           | Firewall/DHCP/DNS for VMs                              |
| SSH Keys          | No password login across all Linux hosts               |
| Cloudflare Tunnel | One per major app or subdomain (bypass CGNAT)          |
| DNS Routing       | Cloudflare-managed custom domains                      |
| Fail2ban + ufw    | Will be added to all VMs                               |
| No exposed ports  | Everything behind tunnels                              |

---

## 6. ☁️ Backup Strategy (rclone)

| Source Path        | Backup Type    | Target            | Frequency |
| ------------------ | -------------- | ----------------- | --------- |
| `/data/postgres`   | Full directory | OneDrive/postgres | Daily     |
| `/data/redis`      | If persistent  | OneDrive/redis    | Daily     |
| `/data/backups`    | Tar.gz dumps   | OneDrive/backups  | Daily     |
| `/etc/docker/`     | Config files   | OneDrive/configs  | Weekly    |
| Huawei video store | Rsync/archive  | OneDrive/videos   | Weekly    |

Use **dedicated rclone remotes** for each major source, or prefix inside one.

---

## 7. 🧠 Notes for Future Reference

* Docker containers in `dev-ubuntu` do **not auto-update** unless you configure watchtower or manually pull.
* Use `docker-compose down && up -d` after edits to restart cleanly.
* Avoid using `latest` tags in production; pin image versions.
* If running heavy Postgres queries, monitor memory pressure on `dev-ubuntu`.
* If future resource strain is noticed, consider:

  * Moving Postgres to its own VM
  * Separating frontend/backend across VMs
* Coolify should not be allowed to control `dev-ubuntu` containers unless deliberate.
* You can run small test apps in Coolify to evaluate it or deploy personal sites.

---

## 8. 📘 Suggested Dev Folder Structure (inside `dev-ubuntu`)

```
/srv/dev/
├── apps/
│   ├── app1/
│   │   ├── frontend/ (Next.js)
│   │   └── backend/  (Express)
│   └── app2/
├── docker/
│   ├── postgres/
│   ├── redis/
│   └── nginx/
├── backups/
└── logs/
```

---

## 9. 🚀 Ready to Deploy

### After Building the VMs:

* Install Docker and Compose in `dev-ubuntu`
* Write `docker-compose.yml` with pinned images
* Setup volumes under `/data/...`
* Create rclone config with encrypted remotes (optional)
* Install Coolify in `coolify-ubuntu` VM as root
* Create and run Cloudflare tunnels via CLI or GUI
* Configure DaVinci and FD-HP to access Huawei over Samba/SSH

