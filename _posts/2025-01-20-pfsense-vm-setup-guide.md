---
layout: post
title: "pfSense Virtual Machine"
subtitle: "Setting Up pfSense and Network Configuration for Proxmox"
date: 2025-01-20 01:00:00 +0530
categories: [Proxmox]
tags: [pfsense, firewall, proxmox]
image: /images/pfsense-vm.png
---



# Setting Up pfSense and Network Configuration for Proxmox

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

### **Step 1: Creating the VM**
- **VM Configuration:**
  - **Machine Type:** q35
  - **BIOS:** SeaBIOS
  - **Disks:** 32GB, SCSI with `write-back` cache.
  - **CPU:** 4 cores, `default` type (not `host`).
  - **Memory:** 4096MB (4GB).
  - **Network:** No network device added during creation due to NIC passthrough.
  - **PCI Device:** Mapped i350 ports using PCI passthrough.
    - Advanced settings: Enabled "All Functions" and selected the PCIe device option, but one of "All Functions" or "ROM Bar" may have been grayed out for mapped devices.
  - Did not start the VM immediately after creation.
  - Enabled "Autostart VM" after completing passthrough configuration.

### **Step 2: Network Preparation**
- Passed through all four ports of the Intel i350 NIC to the pfSense VM.
- Confirmed IOMMU groups and mapped devices correctly.
- **Cables connected:**
  - WAN cable from ISP router to i350 port 1.

### **Step 3: pfSense Installation**
- Uploaded the pfSense ISO to Proxmox.
- Booted the VM from the ISO and completed the installation.
- Configured the WAN interface to obtain an IP address via DHCP (ISP router assigns it).
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
