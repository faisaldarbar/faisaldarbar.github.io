---
title: "Ubuntu Server VM For My Digital Asset Library"
date: 2025-07-16T02:30:00Z
description: "Detailed documentation of the Ubuntu Server VM setup for hosting a private video asset library using Hugo and Proxmox VE."
categories: ["Homelab Docs"]
tags: [ubuntu, server, vm, proxmox]

cover:
  image: "/images/ubuntu-server-vm.png"
  alt: "Ubuntu Server VM"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

## **Project Context**

I'm building a private, searchable asset library for YouTube video production, consisting of tagged A-roll and B-roll 4K clips. The system will run on a VM inside Proxmox VE hosted on a Lenovo ThinkStation P3 Tiny. The site will be managed with Hugo (PaperModest theme) and only accessed locally.

---

## **Hardware & Host Setup**

**Host System:** Lenovo ThinkStation P3 Tiny  
**Hypervisor:** Proxmox VE  
**Primary VMs already present:**

- pfSense (handles LAN, DHCP, firewall)
- Proxmox Backup Server (PBS)

**Target VM Role:** Ubuntu Server for hosting Hugo static site and managing video assets

---

## **VM Configuration Summary**

- **VM Host**: Proxmox VE  
- **Machine Type**: Q35  
- **BIOS**: UEFI (OVMF) with EFI disk on `local-lvm`, no pre-enrolled keys  
- **SCSI Controller**: VirtIO SCSI single  
- **Disk Bus/Device**: VirtIO Block  
- **Disk Cache**: Write back  
- **I/O Thread**: Enabled  
- **CPU**: 1 socket, 4 cores (host type chosen for better passthrough/performance)  
- **Memory**: 8192 MB RAM  

---

## **Network Setup**

- **Bridge**: `vmbr1` (VM-only LAN, 10.0.1.0/24)  
- **Model**: VirtIO  
- **IP assignment**: DHCP via pfSense  
- **Expected VM IP**: 10.0.1.100 (assigned by pfSense)  
- **Gateway/DNS**: 10.0.1.1 (pfSense)  

---

## **Installation Walkthrough**

1. Booted Ubuntu Server 24.04 ISO in VM  
2. Selected guided install → full disk usage (750 GB assigned)  
3. Created user: `faisal`, password set  
4. Chose to enable Ubuntu Pro  
5. Enabled **OpenSSH server** for remote access  
6. Fetched existing GitHub SSH key (associated with Ubuntu desktop originally)  
7. Later, generated a new SSH key via WSL (Windows Subsystem for Linux):  
   - **Type**: ED25519  
   - **GitHub label**: `SSH - WSL (FD-HP) to Ubuntu-Server@PVE`  
8. Chose not to install featured snaps  
9. Completed install → reboot  
10. Removed installation ISO:  
    - Detached CD/DVD device in Proxmox Hardware tab  
11. VM booted to login screen  

---

## **Next Steps**

- [ ] Setup the server with a static IP 

---

---

### 🔐 SSH Key Info (WSL → Ubuntu Server VM)

- **Private key location (WSL host):**  
  `~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve`

- **Public key location (WSL host):**  
  `~/.ssh/id_ssh_wsl_fd-hp_to_ubuntu-server_pve.pub`

- **GitHub label (if added):**  
  `SSH - WSL (FD-HP) to Ubuntu-Server@PVE`

- **Use:**  
  Secure passwordless SSH access from WSL (`faisal@FD-HP`) to Ubuntu Server VM (`10.0.1.100`)

---

## **Network Topology Recap**

```
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

## **Notes**

- Videos will be stored within the VM (likely `/srv/videos` or similar)  
- Most of SSD (750 GB) is allocated to this VM  
- Hugo site is private (no public internet access)  
- Future improvements may include VLAN tagging or VPN access
