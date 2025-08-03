---
title: "Ubuntu Server Virtual Machine on Proxmox VE"
date: 2025-07-16T02:30:00Z
description: "Documentation of my server setup for internal use and reference."
categories: ["Homelab Docs"]
tags: [ubuntu, server, vm, proxmox]

cover:
  image: "/images/ubuntu-server-virtual-machine.webp"
  alt: "Ubuntu Server Virtual Machine on Proxmox VE"
  caption: "Photo by Florian Krumm on Unsplash"

---

### 📁 Project Context

This documentation captures the setup process of an **Ubuntu Server Virtual Machine** hosted on **Proxmox VE**, intended for general-purpose use in my home lab.

---

### 💻 Hardware & Host Setup

- **Host System:** Lenovo ThinkStation P3 Tiny
- **Hypervisor:** Proxmox VE

**Primary VMs already present:**

- pfSense (handles LAN, DHCP, firewall)
- Proxmox Backup Server (PBS)

---

### ⚙️ VM Configuration Summary

- **VM Host:** Proxmox VE
- **Machine Type:** Q35
- **BIOS:** UEFI (OVMF) with EFI disk on local-lvm, no pre-enrolled keys
- **SCSI Controller:** VirtIO SCSI single
- **Disk Bus/Device:** VirtIO Block
- **Disk Cache:** Write back
- **I/O Thread:** Enabled
- **CPU:** 1 socket, 4 cores (host type)
- **Memory:** 8192 MB RAM

---

### 🌐 Network Setup

- **Bridge:** vmbr1 (VM-only LAN, 10.0.1.0/24)
- **Model:** VirtIO
- **IP assignment:** Static via pfSense DHCP reservation
- **Assigned IP:** 10.0.1.10
- **Gateway/DNS:** 10.0.1.1 (pfSense)

---

### 🔍 Installation Walkthrough

- Booted Ubuntu Server 24.04 ISO in VM
- Selected guided install → full disk usage (750 GB assigned)
- Created user: `faisal`, password set
- Enabled Ubuntu Pro
- Enabled OpenSSH server for remote access
- Skipped featured snaps
- Completed install → reboot
- Removed ISO from CD/DVD device in Proxmox
- Server booted successfully

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

### 🧪 Network Isolation Test (pfSense Down)

To verify fallback access:

- **Shutdown pfSense**
- Connected laptop directly to **ISP router** (`172.16.x.x` range)
- **Started Ubuntu Server VM**
  - Boot process paused at:  
    `A start job is running for Wait for Network to be Configured`  
    (via `systemd-networkd-wait-online.service`)
  - Waited ~2 minutes, then boot proceeded normally
- Logged into the server via **Proxmox console**
- Ran `ip a` to confirm:
  - Interface `enp6s18` was **up**, but no IP assigned (expected without pfSense)
- **SSH was not possible** due to lack of IP and network isolation

✅ **Conclusion:**  
Even without pfSense, I can still access the Ubuntu Server through Proxmox console. This validates a reliable fallback path if the firewall is ever down or corrupted.

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

### 🔥 UFW Firewall Configuration

For enhanced security, set up the Uncomplicated Firewall (UFW) and configure some basic rules to limit access:

```bash
sudo apt update
sudo apt install ufw
```

To secure the server while allowing essential services, I configured UFW (Uncomplicated Firewall) with the following rules:

```bash
sudo ufw limit 22/tcp
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

This sets up:

- 🔐 **SSH (port 22)**: Limited to prevent brute-force attacks
- ❌ All other incoming traffic: Denied
- ✅ All outgoing traffic: Allowed

Check the status:

```bash
sudo ufw status
```

Example output:

```text
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     LIMIT       Anywhere
22/tcp (v6)                LIMIT       Anywhere (v6)
```

UFW is now running with safe, minimal exposure.

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
    - Ubuntu Server: 10.0.1.10
```

---

### 🗒️ Notes

- VM allocated 750 GB storage  
- SSH key-based login configured  
- Proxmox console access available as fallback  
- Minimal external exposure using UFW  
