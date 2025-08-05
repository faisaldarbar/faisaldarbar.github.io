---
title: "Samba Setup on Bare Metal (fd-huawei)"
date: 2025-08-04T02:40:00Z
description: "Setting Up a Samba Share on Bare Metal (fd-huawei) Ubuntu Server with Windows Access"
categories: ["Homelab Docs"]
tags: [samba, ubuntu, server]

cover:
  image: "/images/samba-setup-guide.webp"
  alt: "Picture of a Floppy Disk"
  caption: "Photo by benjamin lehman on Unsplash"

---

## 🧾 Summary

Set up a secure, user-authenticated Samba share on `fd-huawei` for access from Windows clients over the LAN. 

---

## ✅ Steps Followed

### 1. Check Partition Mount `/data`

Checked via:

```bash
lsblk
```

### 2. Set Ownership and Permissions

```bash
sudo chown -R faisal:faisal /data
sudo chmod -R 770 /data
```

Ensures only the user `faisal` (and group members) can access the shared files.

---

### 3. Install and Clean Samba Config

Installed if not already:

```bash
sudo apt update
sudo apt install samba
```

Backed up and cleaned the config:

```bash
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
sudo nano /etc/samba/smb.conf
```

Added the share block at the bottom:

```ini
[data]
   path = /data
   browseable = yes
   read only = no
   valid users = faisal
   veto files = /lost+found/
```

✅ `veto files` hides system folders like `lost+found` from Windows clients.

Restarted Samba:

```bash
sudo systemctl restart smbd
```

---

### 4. Create Samba User

Even though the Linux user `faisal` exists, Samba needs its own password entry:

```bash
sudo smbpasswd -a faisal
```

---

### 5. Open Firewall for Samba

Enabled UFW rules for Samba:

```bash
sudo ufw allow 'Samba'
```

UFW status confirmed:

```bash
sudo ufw status
```

Should include:

```
Samba                     ALLOW       Anywhere
```

---

### 6. Map Network Drive from Windows Explorer

Mapped from a Windows PC:

- Open **File Explorer**
- Right-click on **This PC** → **Map Network Drive**
- Choose a drive letter (e.g., `Z:`)
- Folder: `\10.0.0.10\data`
- Check **Reconnect at sign-in**
- Enter credentials:
  - Username: `faisal`
  - Password: [Your Samba password]

---

## 🫥 Hide `lost+found` (Optional)

To hide `lost+found` (ext4 system folder) from Windows:

In `/etc/samba/smb.conf`, within `[data]` block:

```ini
veto files = /lost+found/
```

Then:

```bash
sudo systemctl restart smbd
```

✅ Now `lost+found` is hidden from Windows, but still accessible on Linux.
