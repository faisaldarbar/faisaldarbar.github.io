---
title: "pfSense Virtual Machine"
date: 2025-01-20T02:30:00Z
description: "Setting Up pfSense and Network Configuration for Proxmox"
categories: ["Homelab Docs"]
tags: [pfsense, firewall, proxmox]

cover:
  image: "/images/pfsense-vm.webp"
  alt: "pfSense Virtual Machine"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

This guide documents the step-by-step process I followed to set up pfSense in a virtualized environment using Proxmox, configure a TP-Link router as an access point, and extend the network with another TP-Link router in range extender mode. This document is intended for future reference or anyone attempting a similar setup.

---

## **1. Initial Proxmox Setup**

### **Hardware Configuration:**
- **Host Machine:** Lenovo ThinkStation P3 Tiny
- **Processor:** Intel® Core™ i5-14600 vPro® Processor (E-cores up to 3.90 GHz, P-cores up to 5.20 GHz)
- **NICs:**
  - Intel i219 (single port) used for management.
  - Intel i350-T4 quad-port NIC passed through to pfSense VM.

### **Proxmox Installation:**
- Installed Proxmox VE and ensured all updates were applied.
- Configured the management interface (i219) on `vmbr0`.
  - **IP:** 172.16.0.5/24
  - **Gateway:** 172.16.0.1

---

## **2. pfSense VM Creation on Proxmox**

- Download the installer Netgate Installer - AMD64 ISO IPMI/Virtual Machines
- Extract with 7-Zip since it's a .gz file.
- Upload to Proxmox local (pve).

### **Step 1: Creating the VM**
- **VM Configuration:**
  - **Machine Type:** q35
  - **BIOS:** SeaBIOS
  - **SCSI Controller:** VirtIO SCSI Single
  - **Qemu Agent:** Enabled
  - **Disks:** 32GB, VirtIO Block, local-lvm, discard, backup, IO thread disabled.
  - **CPU:** 2 cores, `host` type.
  - **Memory:** 2048MB (2GB). Since pfSense recommends ZFS, assigning more RAM than required can have a negative impact on your overall memory usage. This can always be adjusted later as needed.
  - **Network:** No network device added during creation due to NIC passthrough.

### **Step 2: Network Preparation**

- **Enabled IOMMU:**  
  - Switch to the root user:  
    ```bash
    su -
    ```
  - Edit the GRUB configuration file:  
    ```bash
    nano /etc/default/grub
    ```
  - Modify the `GRUB_CMDLINE_LINUX_DEFAULT` line to:  
    ```bash
    GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
    ```
  - Update GRUB and reboot:  
    ```bash
    update-grub
    reboot
    ```
  - Verify IOMMU is enabled:  
    ```bash
    dmesg | grep -e DMAR -e IOMMU
    ```

- **Load VFIO Modules (for PCI Passthrough):**  
  - Edit the modules file:  
    ```bash
    nano /etc/modules
    ```
  - Add the following lines:
    ```
    vfio
    vfio_iommu_type1
    vfio_pci
    vfio_virqfd
    ```
  - Update the initramfs and reboot:  
    ```bash
    update-initramfs -u
    reboot
    ```

- **PCI Device:**  
  - Mapped **i350** ports using PCI passthrough.
  - **Advanced settings:**
    - Enabled **"ROM-Bar"**
    - Enabled **"PCI-Express"**

---

### **Final Touches**

- Did **not** start the VM immediately after creation.
- Enabled **"Autostart VM"** after completing passthrough configuration in **pfSense VM Options**.


### **Step 3: pfSense Installation**
- Started the VM.
- Configured the WAN interface to obtain an IP address via DHCP (ISP router assigns it).
- Configured the LAN interface to a static IP and enabled it as a DHCP server handing our IPs in a specified address range.
- Did a few more settings.
- Opened pfSense Admin page in a browser at the static IP.
- Ran through the configuration wizard referring to video from Jim's Garage.
- Disabled "Block RFC1918 Private Networks" as the ISP router assigns a private IP in my case.
- Enabled "Block Bogon Networks" for additional security.

---

## **3. Configuring the TP-Link Archer C6 as an Access Point**

### **Step 1: Initial Configuration**
- Connected the Archer C6 to a laptop via a LAN port.
- Accessed the admin page at `tplinkwifi.net`.
- Set up the router in **Access Point mode**:
  - **LAN IP:** Changed to static `10.0.0.2` (to easily access it).
  - Configured Wi-Fi with a strong password.

### **Step 2: Connecting to the Network**
- Connected the Archer C6’s WAN port to the LAN port passed through to pfSesnse.
- Verified that the Archer C6 received an IP address from pfSense and devices could connect to Wi-Fi.

---

## **4. Configuring the TP-Link C24 in Range Extender Mode**

### **Step 1: Resetting the Device**
- Held the reset button until the lights turned off and restarted.

### **Step 2: Setting Up as a Range Extender**
- Connected the C24 to a laptop via a LAN port.
- Accessed the admin page at `tplinkwifi.net`.
- Selected **Range Extender mode**:
  - Connected to the Archer C6’s Wi-Fi.
  - Set up the same Wi-Fi name and password for seamless roaming.

### **Step 3: Final Connection**
- Placed the C24 in an optimal location to extend Wi-Fi coverage to further corners of my homelab.

---

## **5. Final Network Overview**

### **IP Address Scheme:**
- **pfSense (Gateway):** 10.0.0.1
- **Archer C6 (Access Point):** 10.0.0.2
- **C24 (Range Extender):** 10.0.0.3.

### **Cabling:**
- WAN cable from ISP router to i350 port 1 (assigned to pfSense WAN interface).
- LAN cable from Proxmox host's i350 port 2 (assigned to pfSense LAN interface) to Archer C6 WAN port.
- Management cable from ISP router to i219 port.

---

## **6. Notes and Tips**

1. **pfSense Configuration:**
   - Regularly back up the pfSense configuration and the VM (plan to be devised).
   - Create and monitor firewall rules and logs to ensure proper traffic flow.

2. **Access Point and Range Extender:**
   - Ensure both devices have strong admin passwords, never use only numbers!
   - Use non-overlapping channels for optimal Wi-Fi performance.

3. **Cable Management:**
   - Organize cables for easier troubleshooting.
   - Use proper cable ties to keep the setup tidy.

---

With this setup, the network is optimized for performance, security, and coverage. The Archer C6 serves as the main access point, while the C24 extends Wi-Fi coverage to areas with weak signals. pfSense acts as the central router and firewall, managing traffic effectively.
