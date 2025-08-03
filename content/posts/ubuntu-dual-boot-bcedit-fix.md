---
title: "Ubuntu Dual Boot: BCDEDIT Fix"
date: 2025-02-24T02:30:00Z
description: "If your dual-boot setup always boots into Windows"
categories: ["Windows Docs"]
tags: [windows, linux, dual-boot, bcedit]

cover:
  image: "/images/ubuntu-dual-boot-bcedit-fix.webp"
  alt: "Ubuntu Dual Boot: BCDEDIT Fix"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

If you've installed Ubuntu alongside Windows in a dual-boot setup and find that your system **always boots into Windows**, or you want to **set Ubuntu as the default**, this guide will help you fix it using `bcdedit` in Windows.

## **Issue: Ubuntu Not Showing in Boot Menu**
After installing Ubuntu, some systems boot directly into Windows without showing the GRUB menu. This happens because Windows Boot Manager takes priority over Ubuntu in the UEFI boot order.

## **Solution: Set Ubuntu as Default Boot Entry**
To make Ubuntu the default bootloader, follow these steps:

### **1. Check the Boot Entries**
Open **Command Prompt as Administrator** and type:
```powershell
bcdedit /enum firmware
```
Look for an entry with `path \EFI\ubuntu\shimx64.efi` or `\EFI\ubuntu\grubx64.efi`.

### **2. Change the Boot Order to Ubuntu**
Run the following command to set Ubuntu as the default bootloader:
```powershell
bcdedit /set {bootmgr} path \EFI\ubuntu\shimx64.efi
```

### **3. Restart the System**
After running the command, restart your computer. It should now boot into GRUB, allowing you to choose Ubuntu or Windows.

## **BitLocker Recovery Prompt on First Boot**
If you have **BitLocker enabled**, selecting Windows from GRUB for the first time **may prompt you for a recovery key**. This happens because BitLocker detects a change in the boot sequence. Once entered, Windows should boot normally, and you typically **won’t be asked again**.

## **Fixing Boot Error Messages Before GRUB**
Some users may see an error message before GRUB loads, such as:
```
Failed to open \EFI\ubuntu\...
Failed to load image \EFI\ubuntu\...
Start_image() returned invalid parameters
```
To resolve this, run the following commands in Ubuntu:
```bash
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu
sudo update-grub
```
This properly reinstalls GRUB and updates the bootloader configuration, eliminating the error.

## **Restoring Windows Boot Manager as Default**
If you later decide to remove Ubuntu and want to set Windows as the default bootloader again:

1. **Run the following command in Windows**:
   ```powershell
   bcdedit /set {bootmgr} path \EFI\Microsoft\Boot\bootmgfw.efi
   ```

2. **Restart the system** to confirm that it boots directly into Windows.

3. (Optional) **Remove Ubuntu's Boot Entry from UEFI**:
   ```powershell
   bcdedit /delete {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
   ```
   Replace `{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}` with Ubuntu’s identifier from Step 1.

## **Final Thoughts**
By using the `bcdedit` command, you can control which OS boots by default without entering the BIOS. Additionally, reinstalling GRUB ensures a smooth dual-boot experience without errors. This method is safe and effective for managing dual-boot configurations.

