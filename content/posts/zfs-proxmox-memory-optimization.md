---
title: "ZFS Proxmox Memory Optimization"
date: 2025-01-22T02:30:00Z
description: "Optimizing ZFS for Efficient Memory Usage in Proxmox"
categories: ["Homelab Docs"]
tags: [zfs, memory, proxmox]

cover:
  image: "/images/zfs-proxmox-memory-issue.webp"
  alt: "ZFS Proxmox Memory Optimization"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

In this post, I will share my experience with **optimizing ZFS** on **Proxmox**, specifically how I reduced memory consumption caused by ZFS's **ARC (Adaptive Replacement Cache)**. ZFS is an advanced file system known for its data integrity and scalability, but its memory requirements can sometimes be too high, especially when running on machines with limited RAM. 

I’ll show you how to manage this effectively to prevent ZFS from consuming too much memory, allowing you to run **virtual machines (VMs)** like **pfSense** more efficiently.

---

### **System Overview**
- **Proxmox Version:** 8.3.3  
- **CPU:** Intel® Core™ i5-14600 vPro® Processor 
- **RAM:** 16 GB  
- **Storage:** 1 TB NVMe SSD using ZFS  
- **pfSense VM:** Running pfSense for routing/firewalling tasks  

---

### **Initial Problem: High Memory Usage Due to ZFS ARC**

When I first set up **ZFS** on my Proxmox host, I noticed that ZFS was consuming a large portion of the available **RAM** because of its **ARC (Adaptive Replacement Cache)**, which is designed to improve the performance of the filesystem by caching frequently accessed data. This led to high memory usage, especially when running **multiple virtual machines** on a system with **limited resources**.

Here's a quick overview of the issue:
- **Proxmox Node RAM Usage:** ~50% of 16 GB
- **pfSense VM RAM Usage:** ~80% of 4 GB

---

### **Step 1: Check ZFS Memory Usage**

ZFS's ARC can consume a lot of RAM. Before making any changes, I first checked the **current memory usage by ZFS** to understand how much RAM was being consumed.

#### Check ARC Stats:
To see the current memory usage for **ZFS ARC**, use the following command:

```bash
cat /proc/spl/kstat/zfs/arcstats | grep -E "c|size"
```

This command will provide you with information like the **current cache size** and how much memory ZFS is consuming for its caching mechanism.

---

### **Step 2: Fine-Tune ZFS ARC Memory Usage**

To address the high memory usage, I **fine-tuned ZFS's ARC** to limit how much memory it could consume. This way, more RAM was freed up for other processes, including **virtual machines (VMs)**.

#### How to Adjust the ARC Maximum Size:
1. **Edit the ZFS configuration** to set a maximum size for the ARC:
   - Create or modify the file `/etc/modprobe.d/zfs.conf` and add the following line:

   ```bash
   options zfs zfs_arc_max=1073741824  # 1 GB
   ```

   This limits the ARC's maximum memory usage to **1 GB**, which can be adjusted depending on the total amount of RAM available on your system.

2. **Update the initramfs** and reboot the system:

   ```bash
   update-initramfs -u
   reboot
   ```

   After rebooting, the memory consumption by ZFS will be limited to the value you set in `zfs_arc_max`.

---

### **Step 3: Check New ARC Usage**

After limiting the ARC size, I checked the system again to verify the changes:

1. **Re-check ARC stats**:

   ```bash
   cat /proc/spl/kstat/zfs/arcstats | grep -E "c|size"
   ```

   The values should now reflect the new memory limits set for ARC.

2. **Observe RAM usage** in the **Proxmox GUI** to ensure that the system is no longer being overwhelmed by ZFS memory usage.

---

### **Step 4: Additional Optimizations (Optional)**

If you're running **multiple VMs** and still face resource contention, you can implement further optimizations such as:

- **Reducing RAM allocated to VMs**: For lightweight VMs (like pfSense), reduce the allocated RAM to a minimum required for proper functionality. In my case, I reduced the pfSense VM’s RAM from 4 GB to 2 GB, which improved its performance without causing issues.
  
- **Changing CPU type to "host" for VMs**: This ensures the VM gets better performance by leveraging the host CPU's full capabilities. 

---

### **Step 5: Monitoring Results**

After these optimizations, I noticed significant improvements:

- **Proxmox Host:**
  - **RAM Usage:** 25.01% (3.82 GiB of 15.26 GiB)
  - **CPU Usage:** 0.13% of 20 CPUs (virtually idle)

- **pfSense VM:**
  - **RAM Usage:** 77.90% (1.56 GiB of 2.00 GiB)
  - **CPU Usage:** 2.58% of 1 CPU(s)

This was a dramatic improvement in both **CPU** and **RAM usage**, as the **Proxmox host** and the **pfSense VM** were now efficiently utilizing system resources.

---

### **Conclusion**

By fine-tuning **ZFS ARC**, I was able to significantly reduce memory usage on my Proxmox host, allowing me to run multiple **VMs** more efficiently on a system with limited resources. 

If you're using **ZFS** on your Proxmox setup and experiencing high memory consumption, the steps outlined in this guide should help you manage ARC memory more effectively, preventing ZFS from overwhelming your system. Additionally, ensuring that your **VMs** are allocated only as much memory and CPU as they need will ensure that your **Proxmox host** runs smoothly.

---

### **Additional Recommendations**
- **Monitor your system regularly** using Proxmox's built-in monitoring tools to make sure you're not running into resource contention.
- **Consider using a smaller storage pool for VMs** if you're not using ZFS for production workloads, as ZFS can be overkill in such scenarios.

I hope this guide helps you get your **Proxmox** and **ZFS** setup running efficiently. Feel free to leave a comment or contact me if you have questions or need further clarification!
