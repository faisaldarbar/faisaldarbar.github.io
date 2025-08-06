---
title: "Ubuntu Server on Bare Metal (fd-huawei)"
date: 2025-08-04T02:30:00Z
description: "Documentation of my server setup for internal use and reference."
categories: ["Homelab Docs"]
tags: [ubuntu, server, bare metal]

cover:
  image: "/images/ubuntu-server-virtual-machine.webp"
  alt: "Ubuntu Server on Bare Metal (fd-huawei)"
  caption: "Photo by Florian Krumm on Unsplash"

---

> Clean bare-metal install of Ubuntu Server 24.04 on my Huawei laptop (`fd-huawei`), intended for secure network services and local file hosting.

---

## 📁 Project Context

This machine runs Ubuntu Server 24.04 directly on hardware (not virtualized). It serves as a general-purpose node in my home lab — mainly for Samba file sharing.

---

## 💻 Hardware & Installation

* **Machine:** Huawei MateBook D15 (bare metal)
* **CPU/RAM:** Native specs (non-virtualized)
* **Boot Mode:** UEFI
* **Install Disk:** Full disk used (\~238 GB NVMe)
* **OS:** Ubuntu Server 24.04.2 LTS
* **Filesystem Layout:**

  ```bash
  /         → 30 GB
  /boot     → 1 GB
  /boot/efi → 1 GB
  /data     → 206.4 GB (separate partition for Samba share)
  ```

---

## 🌐 Network Setup

* **Static IP (via pfSense DHCP reservation):** `10.0.0.10`
* **Gateway/DNS:** `10.0.0.1` (pfSense LAN)
* **Client Subnet:** `10.0.0.0/24`

💡 Verified static assignment by:

```bash
ip a
```

---

## 🔐 SSH Access Setup (WSL → fd-huawei)

Secure, passwordless SSH access from WSL (`faisal@FD-HP`) to the `fd-huawei` server:

### 1. SSH Key Generation (WSL)

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ssh_wsl_fd-hp_to_fd-huawei
```

* Private key: `~/.ssh/id_ssh_wsl_fd-hp_to_fd-huawei`
* Public key: `~/.ssh/id_ssh_wsl_fd-hp_to_fd-huawei.pub`

### 2. Copy Public Key to fd-huawei (Server)

Still on fd-hp, run:

```bash
ssh-copy-id -i ~/.ssh/id_ssh_wsl_fd-hp_to_fd-huawei.pub faisal@10.0.0.10
```

### 3. SSH Config Setup (WSL)

Edited `~/.ssh/config`:

```ssh
# Alias for Ubuntu Server on fd-huawei
Host fd-huawei
    HostName 10.0.0.10
    User faisal
    IdentityFile ~/.ssh/id_ssh_wsl_fd-hp_to_fd-huawei

# Optional: direct IP usage without needing an alias
Host 10.0.0.10
    User faisal
    IdentityFile ~/.ssh/id_ssh_wsl_fd-hp_to_fd-huawei
```

✅ Can now SSH via:

```bash
ssh fd-huawei
```

### 4. SSHD Config (on fd-huawei)

Edited `/etc/ssh/sshd_config`:

```text
PasswordAuthentication no
PubkeyAuthentication yes
```

Then restarted the service:

```bash
sudo systemctl restart ssh
```

✅ SSH key-only login is now enforced.


### 5. Remove Cloud-Init SSH Override

After setup, SSH password login was still enabled — despite disabling it in `sshd_config`.

This was detected when using another system where the SSH private key was not present.

Upon inspection, the following file existed on the server:

```bash
/etc/ssh/sshd_config.d/50-cloud-init.conf
```

It contained this line:

```bash
PasswordAuthentication yes
```

✅ To ensure password-based SSH login remains disabled:

```bash
sudo rm /etc/ssh/sshd_config.d/50-cloud-init.conf
```

Then verify:

```bash
sudo sshd -T | grep password
```

Expected:

```
passwordauthentication no
```

Finally, restart the SSH service:

```bash
sudo systemctl restart ssh
```

This ensures `sshd_config` is not silently overridden by leftover cloud-init configs after reboot.

🎥 Watch the video version:

{{< youtube jtW8UoX06Mw >}}

---

## 🔥 UFW Firewall Configuration

For basic protection, UFW (Uncomplicated Firewall) is used:

### Commands Run:

```bash
sudo apt update
sudo apt install ufw
sudo ufw limit 22/tcp
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

### Status Check:

```bash
sudo ufw status
```

Output:

```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     LIMIT       Anywhere
22/tcp (v6)                LIMIT       Anywhere (v6)
```

✅ Ensures SSH access is limited while all other incoming traffic is denied.

---

## 🌐 Network Topology Recap

| Component     | IP Address | Notes                    |
| ------------- | ---------- | ------------------------ |
| ISP Router    | 172.16.0.1 | Gateway to Internet      |
| pfSense WAN   | 172.16.x.x | WAN interface            |
| pfSense LAN   | 10.0.0.1   | DHCP/Firewall            |
| Archer C6     | 10.0.0.2   | Wireless Access Point    |
| fd-huawei     | 10.0.0.10  | Bare-metal Ubuntu server |

---

## 🗒️ Notes

* Server is installed bare metal
* Static IP assigned via pfSense DHCP reservation
* SSH key-only login configured via `sshd_config`
* UFW firewall configured with minimal rules