---
title: "Diskpart Disk Cleanup"
date: 2023-10-15T02:30:00Z
description: "Manage disks and partitions in Windows"
categories: ["Windows Docs"]
tags: [windows, diskpart, format, cleanup]

cover:
  image: "/images/diskpart.webp"
  alt: "Diskpart Disk Cleanup"
  caption: "Photo by amt8u on Unsplash"

---

Diskpart is a powerful command-line utility in Windows for managing disks and partitions. If you need to completely format and clean up a disk using Diskpart, follow these steps carefully, as these operations can result in data loss.

**Warning: Be absolutely certain that you are selecting the correct disk because the data on the selected disk will be irrecoverably deleted. Double-check your commands before executing them.**

## 1. **Open Command Prompt as Administrator**:

   - Press `Win + X` and choose "Command Prompt (Admin)" or "Windows PowerShell (Admin)" if you're using Windows 10 or 11.

## 2. **Launch Diskpart**:

   - In the Command Prompt window, type `diskpart` and press Enter. This will launch the Diskpart utility.

## 3. **List Disks**:

   - Type the following command to display a list of all the disks on your computer. Identify the disk number that corresponds to the one you want to format and clean up:

     ```shell
     list disk
     ```

## 4. **Select the Disk**:

   - Use the following command to select the disk you want to format and clean up. Replace "X" with the actual disk number:

     ```shell
     select disk X
     ```

## 5. **Clean the Disk**:

   - Type the following command to remove all partitions and volumes on the selected disk:

     ```shell
     clean
     ```

## 6. **Create a New Partition (Optional)**:

   - If you want to create a new partition on the cleaned disk, you can use the following command:

     ```shell
     create partition primary
     ```

## 7. **Format the Partition (Optional)**:

   - If you created a new partition, you can format it with the following command. This example formats the partition as NTFS, but you can replace "ntfs" with "fat32" or other file systems if desired:

     ```shell
     format fs=ntfs quick
     ```

## 8. **Assign a Drive Letter (Optional)**:

   - If you want to assign a drive letter to the partition, use the following command to assign a specific drive letter (replace "X" with the desired letter):

     ```shell
     assign letter=X
     ```

## 9. **Exit Diskpart**:

   - Type `exit` to exit the Diskpart utility.

## 10. **Exit Command Prompt**:

   - Type `exit` again to close the Command Prompt.

Your selected disk should now be completely cleaned, and if you created a new partition, it will be formatted and assigned a drive letter. Be cautious and ensure that you are working on the correct disk to avoid accidental data loss.
