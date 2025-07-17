---
title: "Ubuntu Server VM For My Digital Asset Library"
date: 2025-07-16T02:30:00Z
description: "Detailed documentation of the Ubuntu Server VM setup for hosting a private video asset library using Hugo and Proxmox VE."
categories: ["Homelab Docs"]
tags: [ubuntu, server, vm, proxmox]

cover:
  image: "/images/ubuntu-server-vm.jpg"
  alt: "Ubuntu Server VM"
  caption: "Photo by Florian Krumm on Unsplash"

---

### 📁 Project Context

I’m building a private, searchable asset library for YouTube video production, consisting of tagged A-roll and B-roll 4K clips. The system will run on a VM inside Proxmox VE hosted on a Lenovo ThinkStation P3 Tiny. The site will be managed with Hugo (PaperModest theme) and only accessed locally.

---

### 💻 Hardware & Host Setup

- **Host System:** Lenovo ThinkStation P3 Tiny
- **Hypervisor:** Proxmox VE

**Primary VMs already present:**

- pfSense (handles LAN, DHCP, firewall)
- Proxmox Backup Server (PBS)

**Target VM Role:** Ubuntu Server for hosting Hugo static site and managing video assets

---

### ⚙️ VM Configuration Summary

- **VM Host:** Proxmox VE
- **Machine Type:** Q35
- **BIOS:** UEFI (OVMF) with EFI disk on local-lvm, no pre-enrolled keys
- **SCSI Controller:** VirtIO SCSI single
- **Disk Bus/Device:** VirtIO Block
- **Disk Cache:** Write back
- **I/O Thread:** Enabled
- **CPU:** 1 socket, 4 cores (host type chosen for better passthrough/performance)
- **Memory:** 8192 MB RAM

---

### 🌐 Network Setup

- **Bridge:** vmbr1 (VM-only LAN, 10.0.1.0/24)
- **Model:** VirtIO
- **IP assignment:** DHCP via pfSense
- **Expected VM IP:** 10.0.1.100 (assigned by pfSense)
- **Gateway/DNS:** 10.0.1.1 (pfSense)

---

### 🔍 Installation Walkthrough

- Booted Ubuntu Server 24.04 ISO in VM
- Selected guided install → full disk usage (750 GB assigned)
- Created user: `faisal`, password set
- Enabled Ubuntu Pro
- Enabled OpenSSH server for remote access
- Chose not to install featured snaps
- Completed install → reboot
- Removed installation ISO by detaching CD/DVD device in Proxmox Hardware tab
- VM booted to login screen

---

### 🛁 Static IP Configuration via pfSense

To ensure the Ubuntu Server VM always receives the same IP address, I created a **DHCP static lease** in pfSense:

- **Reserved IP:** `10.0.1.10`
- Chosen outside the DHCP pool range (`10.0.1.100 – 10.0.1.200`) but within the `10.0.1.0/24` subnet.
- Static lease assigned based on the VM's MAC address (viewable in Proxmox or with `ip a`)

After applying the lease in pfSense, I rebooted the VM.

To confirm the IP assignment, I ran:

```bash
ip a
```

Confirmed that the interface (e.g., `enp6s18`) received the static IP:

```text
inet 10.0.1.10/24 ...
```

Then updated my SSH config on WSL to reflect the new IP:

```bash
Host ubuntu-server-pve
    HostName 10.0.1.10
    User faisal
    IdentityFile ~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve
```

SSH connectivity continues to work seamlessly using the updated IP.

---

### 🔐 SSH Access Setup (WSL → Ubuntu Server VM)

To enable secure, passwordless SSH access from my WSL environment (`faisal@FD-HP`) to the Ubuntu Server VM (`10.0.1.100`), I followed these steps:

#### 1. SSH Key Generation

From WSL:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve -C "SSH - WSL (FD-HP) to Ubuntu-Server@PVE"
```

This created:

- **Private key:** `~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve`
- **Public key:** `~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve.pub`

#### 2. Key Added to GitHub

I added the **public key** to my GitHub account under SSH keys:

- **Label:** `SSH - WSL (FD-HP) to Ubuntu-Server@PVE`

During the Ubuntu Server installation, GitHub keys were automatically imported via cloud-init. This made SSH access work immediately after boot.

> 🔁 Later, I **renamed** the key file locally for better identification. Since the key had already been added to GitHub, renaming didn’t break anything.

#### 3. SSH Config Setup (WSL)

To simplify the SSH command, I added this block to `~/.ssh/config` on WSL:

```bash
Host ubuntu-server-pve
    HostName 10.0.1.10
    User faisal
    IdentityFile ~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve
```

Now I can connect using:

```bash
ssh ubuntu-server-pve
```

#### 4. SSHD Config (on Ubuntu Server)

To ensure the server only accepts key-based logins:

In `/etc/ssh/sshd_config`:

```bash
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH service:

```bash
sudo systemctl restart ssh
```

SSH is now fully configured and working securely between WSL and the Ubuntu Server VM.

---

### 📌 Next Steps

- [x] Verify SSH key-based login from WSL
- [x] Setup the server with a static IP
- [ ] Configure Hugo and start asset organization

---

### 🌐 Network Topology Recap

```text
ISP Router:      172.16.0.1
Proxmox VE:      172.16.0.5
pfSense WAN:     172.16.0.x
pfSense LAN:     10.0.0.1
TP-Link Wi-Fi:   10.0.0.2
VMLAN (vmbr1):   10.0.1.0/24
    - pfSense VMLAN gateway: 10.0.1.1
    - Ubuntu Server: 10.0.1.100 (TBD)
```

---

### 🗒️ Notes

- Videos will be stored within the VM (likely `/srv/videos` or similar)
- Most of SSD (750 GB) is allocated to this VM
- Hugo site is private (no public internet access)
- Future improvements may include VLAN tagging or VPN access
