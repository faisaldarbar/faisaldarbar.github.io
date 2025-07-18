---
title: "Samba Setup Guide"
date: 2025-07-17T02:30:00Z
description: "Setting Up a Samba Share on Ubuntu Server with LVM and Windows Access"
categories: ["Homelab Docs"]
tags: [samba, ubuntu, server, lvm]

cover:
  image: "/images/samba-setup-guide.jpg"
  alt: "Picture of a Floppy Disk"
  caption: "Photo by benjamin lehman on Unsplash"

---

This guide walks through setting up a dedicated logical volume, formatting it, mounting it, configuring Samba, and accessing it from a Windows client — using user-based authentication.

---

## ✅ 1. Check Available Space in Your Volume Group

```bash
sudo vgdisplay
```

Look for the **Free PE / Size** — it should show free space (e.g., ~647 GB).

---

## ✅ 2. Create a Logical Volume

```bash
sudo lvcreate -l 100%FREE -n data-lv ubuntu-vg
```

This creates a logical volume named `data-lv` using all available free space.

---

## ✅ 3. Format the Logical Volume

```bash
sudo mkfs.ext4 /dev/ubuntu-vg/data-lv
```

Formats the new logical volume with an ext4 filesystem.

---

## ✅ 4. Mount the New Volume

```bash
sudo mkdir /data
sudo mount /dev/ubuntu-vg/data-lv /data
df -h /data
```

Verify the new volume is mounted and check available space.

---

## ✅ 5. Reclaim Reserved Space for Root

You may see slightly less space available (e.g., 604G instead of 636G).

By default, ext4 reserves 5% of space for root. To reclaim it:

```bash
sudo tune2fs -m 0 /dev/ubuntu-vg/data-lv
```

🔍 **Why?**  
This command sets the reserved block percentage to 0%, giving back all usable space to users. Ideal for dedicated data drives.

---

## ✅ 6. Configure Auto-Mount in `/etc/fstab`

Get the UUID:

```bash
sudo blkid /dev/ubuntu-vg/data-lv
```

Edit fstab:

```bash
sudo nano /etc/fstab
```

Add this line (replace UUID with your actual one):

```fstab
UUID=e22f2118-6818-4a4c-8dae-c5b885c5f02d /data ext4 defaults 0 2
```

Apply the changes:

```bash
sudo mount -a
```

---

## ✅ 7. Set Permissions for Samba Access

Set ownership and permissions to your Linux user:

```bash
sudo chown -R faisal:faisal /data
sudo chmod -R 770 /data
```

---

## ✅ 8. Restore a Clean Samba Configuration

If you previously modified `/etc/samba/smb.conf`, restore a clean one:

```bash
sudo cp /etc/samba/smb.conf.bak /etc/samba/smb.conf
```

Or reinstall Samba defaults if necessary.

---

## ✅ 9. Create a Samba User

```bash
sudo smbpasswd -a faisal
```

Set a password when prompted.

---

## ✅ 10. Configure Samba Share

Edit the Samba config:

```bash
sudo nano /etc/samba/smb.conf
```

At the bottom, add:

```ini
[data]
   path = /data
   browseable = yes
   read only = no
   valid users = faisal
```

Then restart the Samba service:

```bash
sudo systemctl restart smbd
```

---

## ✅ 11. Open Firewall Port (if applicable)

If you're using UFW (Uncomplicated Firewall):

```bash
sudo ufw allow 'Samba'
```

---

## ✅ 12. Map Network Drive from Windows Explorer

- Open **File Explorer**
- Right-click on **This PC** → **Map Network Drive**
- Choose a drive letter (e.g., `Z:`)
- Folder: `\10.0.1.10\data`
- Check **Reconnect at sign-in**
- Enter credentials:
  - Username: `faisal`
  - Password: [Your Samba password]

You're right — Markdown blocks should be formatted clearly **without mixing** triple backticks (\`\`\`) with normal text unless you're including **code inside the Markdown**.

Here's a **clean and correct `.md` snippet** for your documentation, with proper formatting:

---

## 🫥 Optional: Fully Hide `lost+found` from Windows Clients via Samba

When you format a drive with the ext4 filesystem, Linux creates a `lost+found` folder at the root. This is used internally by the system for recovering file fragments during disk checks (`fsck`). While it's essential for the filesystem, it's not useful for end users — and may clutter your view when accessing the share from Windows.

If you'd like to **completely hide** this folder from Windows (so it doesn't appear even with "Show hidden files" enabled), use Samba's `veto files` option.

---

#### 🔧 Update Your Samba Configuration

1. Open your Samba configuration file:

   ```bash
   sudo nano /etc/samba/smb.conf
   ```

2. In your `[data]` share section, add the `veto files` line (and remove or comment out any `hide files` line if present):

   ```ini
   [data]
   path = /data
   valid users = faisal
   read only = no
   browsable = yes
   veto files = /lost+found/
   ```

3. Save and exit the file, then restart Samba:

   ```bash
   sudo systemctl restart smbd
   ```

---

### ✅ Result

* `lost+found` will be **completely hidden** from Windows clients.
* It remains fully accessible and functional on the Linux system (as required).
* This is a safe and clean way to reduce visual clutter in your Samba share.

Done! ✅

---

## 🎉 You're all set!
You now have a clean and secure user-authenticated Samba share accessible from Windows.
