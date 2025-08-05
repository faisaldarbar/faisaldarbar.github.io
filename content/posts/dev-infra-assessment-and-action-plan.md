---
title: "Dev Infra Assessment and Action Plan"
date: 2025-08-01T02:30:00Z
description: "Lenovo ThinkStation P3 Tiny (Proxmox VE Host)"
categories: ["Dev Docs"]
tags: [proxmox, self-hosted, paas, coolify, ubuntu server, next.js, devops, homelab, lenovo thinkstation, cloudflare tunnel, docker, postgres, react, web development, hugo, virtualization, pbs, backups, self-hosting]


cover:
  image: "/images/dev-infra-assessment-and-action-plan.webp"
  alt: "Dev Infra Assessment and Action Plan"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"
---

## Note

Faisal Darbar from the future here. This action plan has been superseded with the following plan.

[Unified Full-Stack Infrastructure & DevOps Blueprint](/posts/unified-full-stack-infrastructure-and-devops-blueprint)

## 🧭 Purpose

This document outlines the current state, design decisions, and action plan for my personal developer infrastructure.

The motivation for self-hosting arises from the unpredictable costs of Vercel, Netlify, Supabase, and other cloud PaaS services — which can scale suddenly without providing long-term control. Since my current hardware (a high-spec ThinkStation P3 Tiny) outperforms most entry-level VPS offerings, this infrastructure gives me both cost savings and performance headroom.

In the future, I may even scale this setup to offer private cloud services to selected clients.

---

🎥 Watch the video version:

{{< youtube UItxEA5PUNo >}}

---

## 🖥️ Host System Overview

**Device**: Lenovo ThinkStation P3 Tiny  
**Model**: 30H1CTO1WW  
**CPU**: Intel Core i5-14600 (20 threads)  
**RAM**: 16GB DDR5  
**Storage**: 1TB NVMe PCIe Gen4 SSD  
**NICs**:
- Integrated Ethernet
- Intel I350-T4 Quad Port Gigabit Server Adapter

**Proxmox VE** installed with **ext4** (not ZFS for RAM efficiency).

---

## 🧩 Virtual Machine Layout

| VM Name     | vCPU | RAM   | Disk          | Role                    | Notes |
|-------------|------|-------|----------------|--------------------------|-------|
| pfSense     | 2    | 2GB   | 32GB           | Firewall / Router       | Always on |
| Ubuntu Server | 4  | 12GB  | 100GB root + 647GB `/data` | Web App Dev Server | Always on |
| PBS         | 2    | 4GB   | 16GB boot + 104GB backup | Backup Server         | Only powered on during manual backups |

---

## 💽 Storage Strategy

The Ubuntu VM has two logical volumes:

| Mount Point | Size   | Usage | Purpose                              |
|-------------|--------|-------|--------------------------------------|
| `/`         | 100GB  | ~8GB  | OS, Docker, Coolify, tools           |
| `/data`     | 647GB  | ~1.2GB| App volumes, DB data, digital assets |

### `/data` Folder Structure

Organized for both app and media usage:

```
/data/
├── content/        # YouTube videos, thumbnails, images
├── docker/         # Docker volume storage
├── postgres/       # PostgreSQL data
├── redis/          # Redis data
├── backups/        # (Optional) app backups or exports
```

This shared-use model avoids the risk of filling root (`/`) and provides easy separation of concerns.

---

## 🧠 Memory Plan

- Ubuntu VM receives **12GB RAM**
- Remaining RAM reserved for pfSense and occasional PBS usage
- Plan to **upgrade to 32GB** in the future for expanded services and smoother multitasking

---

## 🧮 CPU Plan

- Ubuntu VM currently allocated 4 vCPUs
- Can be increased to 6–8 vCPUs as app workload grows
- Ensure pfSense (2 vCPUs) always has headroom for reliable routing

---

## 🐳 Docker Setup

Docker's default storage location will be moved to `/data/docker` for safety and scalability.

### Docker Config (`/etc/docker/daemon.json`)
```json
{
  "data-root": "/data/docker"
}
```

This avoids bloat in `/` and aligns all app-related data under `/data`.

---

## 🗃️ Database Hosting

For now, **PostgreSQL and Redis** will run inside the Ubuntu VM using Docker, with their volumes mapped to:

```
/data/postgres/
└── db data, configs, backups

/data/redis/
└── cache and session storage
```

In the future, they can be moved to their own VMs if scaling requires isolation.

---

## 🔁 Backup Strategy

### Proxmox Backup Server (PBS)

- **Back up only the root (`/`) disk (100GB)**
- **Exclude** the large `/data` disk from PBS (to reduce backup size and avoid media bloat)
- Manual backups will be taken after milestones or major app deployments

### Offsite Backup for `/data`

- Use `rclone` to sync `/data/content` and other folders to **OneDrive** (~930GB available)
- Consider scripting and scheduling regular syncs in the future

---

## 🔧 Swap Behavior

Ubuntu VM uses default swapfile behavior. No custom changes are made for now. Swap is left **enabled** to support memory burst protection in limited RAM conditions.

---

## ✅ Action Plan

| Area | Action |
|------|--------|
| RAM  | Allocate 12GB to Ubuntu VM |
| CPU  | Scale Ubuntu VM to 6–8 vCPUs as needed |
| Docker | Move Docker volumes to `/data/docker` |
| PBS | Set up root-only backups for Ubuntu VM |
| `/data` | Organize folders for DB, Docker, and media content |
| rclone | Set up periodic sync of `/data/content` to OneDrive |
| Backups | Perform PBS backups manually after important changes |
| Future RAM | Upgrade to 32GB when budget allows |
| Database | Run Postgres & Redis in Ubuntu VM via Docker for now |
| Monitoring | Planned for later (Netdata, Glances, etc.) |
| Security | To be handled in a separate security-focused doc |
| Cloudflare | Tunnel setup will be documented separately |

---

## 📌 Notes

- This setup balances performance, flexibility, and cost-efficiency.
- PBS will remain manual until automation is required.
- Coolify will be the primary PaaS layer for Next.js apps.
- When needed, additional VMs (databases, testing) can be spun up on-demand.

---

## 🔮 Final Thought

This setup is designed to enable app development, hosting, and digital asset management on a stable, powerful local system — avoiding cloud vendor surprises while laying the groundwork for future scaling, client hosting, or even a private cloud offering.