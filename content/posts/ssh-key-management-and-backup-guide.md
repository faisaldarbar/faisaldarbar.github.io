---
title: "SSH Key Management and Backup Guide"
date: 2025-08-04T02:50:00Z
description: "Secure SSH key handling with encrypted, restorable backups."
categories: ["Homelab Docs"]
tags: [ssh, ssh-keys, wsl, encryption, gpg, passphrase, backup, onedrive, linux, windows, key-management, secure-access, homelab, sysadmin, ssh-security, cloud-backup]

cover:
  image: "/images/ssh-key-management-and-backup-guide.webp"
  alt: "SSH Key Management and Backup Guide"
  caption: "Photo by Jason D on Unsplash"

---

## 1. 🔑 Key Generation and Storage

* SSH keys are generated inside **WSL** using `ssh-keygen`.
* Initially, no passphrases were set, but later added using:

  ```bash
  ssh-keygen -p -f ~/.ssh/id_keyname
  ```
* Keys are stored in:

  ```bash
  ~/.ssh/
  ```

  Example key filenames:

  ```bash
  id_ssh_wsl_fd-hp_to_fd-huawei
  id_ssh_wsl_fd-hp_to_ubuntu-server_pve
  ```

---

## 2. 🔐 Encryption with GPG

* Keys are additionally encrypted with GPG (symmetric encryption):

  ```bash
  gpg -c ~/.ssh/id_keyname
  ```
* This creates `.gpg` files like:

  ```bash
  id_ssh_wsl_fd-hp_to_fd-huawei.gpg
  ```
* The `.gpg` files are password-protected with a **separate GPG passphrase**.
* The original unencrypted keys remain in place.

---

## 3. ☁️ Backup Strategy

* Encrypted `.gpg` files are manually backed up to a **OneDrive-synced Windows folder**.
* Path on Windows:

  ```bash
  C:\Users\faisa\OneDrive - Faisal Darbar\Documents\Business\Faisal Darbar\Homelab\SSH-Key-Backups
  ```
* Path from WSL:

  ```bash
  /mnt/c/Users/faisa/OneDrive\ -\ Faisal\ Darbar/Documents/Business/Faisal\ Darbar/Homelab/SSH-Key-Backups/
  ```
* Backup command:

  ```bash
  cp ~/.ssh/*.gpg /mnt/c/Users/faisa/OneDrive\ -\ Faisal\ Darbar/Documents/Business/Faisal\ Darbar/Homelab/SSH-Key-Backups/
  ```
* ⚠️ This **silently overwrites** existing `.gpg` files with the same name.

---

## 4. ♻️ Restore Procedure

To restore a key:

1. Copy the `.gpg` file from your OneDrive folder back into WSL.
2. Decrypt it:

   ```bash
   gpg -d id_keyname.gpg > id_keyname
   ```
3. Set correct permissions:

   ```bash
   chmod 600 id_keyname
   ```
4. Use the key as usual with `ssh` or add it to your SSH config.

---

## 5. 🔒 Security Best Practices

* Private keys are:

  * **Passphrase-protected**
  * **Encrypted with GPG**
  * **Backed up off-WSL on the Windows side, synced to OneDrive**
* Public keys and known hosts do not need encryption.
* You are now protected against:

  * WSL corruption
  * Laptop hardware failure
  * OS reinstall or data loss

---

## 6. 🧠 Maintenance Tips

* Manually re-encrypt any **new keys** you generate:

  ```bash
  gpg -c ~/.ssh/new_key
  ```
* Then run your backup `cp` command again.
* Periodically test a restore by decrypting one key.


