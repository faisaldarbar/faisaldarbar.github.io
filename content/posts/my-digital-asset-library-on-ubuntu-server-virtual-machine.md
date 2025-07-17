---
title: "My Digital Asset Library on Ubuntu Server Virtual Machine"
date: 2025-07-16T02:30:00Z
description: "Documentation of the Ubuntu Server Virtual Machine setup for hosting a Private Digital Asset Library using Hugo and Proxmox VE."
categories: ["Homelab Docs"]
tags: [ubuntu, server, vm, proxmox, digital asset library]

cover:
  image: "/images/digital-asset-library.jpg"
  alt: "My Digital Asset Library"
  caption: "Photo by Florian Krumm on Unsplash"

---

### 📁 Project Context

I’m building a private, searchable digital asset library for YouTube video production, consisting of tagged A-roll and B-roll 4K clips. The system will run on a VM inside Proxmox VE hosted on a Lenovo ThinkStation P3 Tiny. The site will be managed with Hugo (PaperModest theme) and only accessed locally.

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
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

This sets up:

- 🔐 **SSH (port 22)**: Limited to prevent brute-force attacks
- 🌐 **HTTP (port 80)**: Allowed for serving Hugo locally
- 🔒 **HTTPS (port 443)**: Allowed for future use (e.g., self-signed certs or proxy setups)
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
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
22/tcp (v6)                LIMIT       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
443/tcp (v6)               ALLOW       Anywhere (v6)
```

UFW is now running with safe, minimal exposure.

### 🧱 GitHub Repository & SSH Key Setup

1. **Create a Private GitHub Repo**

   - Repo name: `digital-assets-library`

2. **Generate SSH Key (Ubuntu server to GitHub)**

   ```bash
   ssh-keygen -t ed25519 -C "ubuntu-server-pve to github" -f ~/.ssh/id_ssh_ubuntu-server-pve_to_github
   
   ```

3. **Add the public key to GitHub**

   - Copy it: `cat ~/.ssh/id_ssh_ubuntu-server-pve_to_github.pub`
   - Go to GitHub → Settings → SSH and GPG keys → Add new SSH key

4. **Configure SSH for GitHub** (optional for clarity)

   ```bash
   nano ~/.ssh/config
   ```

   Add:

   ```
   Host github.com
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ssh_ubuntu-server-pve_to_github
   ```

5. **Test connection**

   ```bash
   ssh -T git@github.com
   ```

6. **Clone repo**

   ```bash
   git clone git@github.com:faisaldarbar/digital-assets-library.git
   cd digital-assets-library
   ```

---

### 🚀 Hugo Installation and Project Setup

1. **Install Hugo Extended (v0.147.0)**

   ```bash
   wget https://github.com/gohugoio/hugo/releases/download/v0.147.0/hugo_extended_0.147.0_linux-amd64.tar.gz
   tar -xvzf hugo_extended_0.147.0_linux-amd64.tar.gz
   sudo mv hugo /usr/local/bin/
   hugo version
   ```

2. **Create Hugo Site in Cloned Repo**

   ```bash
   hugo new site . --force --format yaml
   ```

---

### 🎨 Add PaperModest Theme

1. **Add as Git Submodule** (read-only)

   ```bash
   git submodule add https://github.com/ianrodrigues/hugo-PaperModest.git themes/PaperModest
   ```

2. **Edit **``

   ```yaml
   theme: PaperModest
   baseURL: http://10.0.1.10:1313/
   languageCode: en-us
   title: "Digital Assets Library"
   ```

---

### 🌍 Allow Hugo's port in UFW

1. **Default 1313**

   ```bash
   sudo ufw allow 1313
   ```

### 🧾 Commit and Push Site to GitHub

Git Identity Configuration before committing to GitHub:

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

Verify:

```bash
git config --list
```

From inside the Hugo site directory:

```bash
git add .
git commit -m "Initialize Hugo site with PaperModest theme"
git push origin main
```

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

- Videos will be stored within the VM (likely `/videos` or similar)
- Most of SSD (750 GB) is allocated to this VM
- Hugo site is private (no public internet access)
- Future improvements may include VLAN tagging or VPN access

---

### 🌍 Serve the Site on LAN

1. **Run Hugo Server**

   From the local network:

   ```bash
   ssh ubuntu-server-pve
   cd projects/digital-assets-library/
   hugo server --buildDrafts --bind 0.0.0.0 --baseURL http://10.0.1.10:1313/
   ```

   Access from browser on LAN: [http://10.0.1.10:1313/](http://10.0.1.10:1313/)
