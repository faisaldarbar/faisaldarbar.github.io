---
title: "VM-Only Network"
date: 2025-02-10T02:30:00Z
description: "A dedicated network for Virtual Machines (VMs) - (VMLAN) with pfSense on Proxmox"
categories: ["Homelab Docs"]
tags: [network, vm, vmbr]

cover:
  image: "/images/vm-only-network.webp"
  alt: "VM-Only Network"
  caption: "Photo by Jordan Harrison on Unsplash"

---

In this guide, I will walk through the steps to set up a dedicated VM-only network (VMLAN) using **pfSense** as the router on **Proxmox**. This setup allows VMs to have their own subnet while being managed by pfSense for DHCP, firewall rules, and potential VLAN segmentation in the future.

---

## **Network Setup Overview**

### **Current Network Configuration:**
- **ISP Router:** `172.16.0.1`
- **Proxmox Host:** `172.16.0.5`
- **pfSense VM (WAN Interface - DHCP on ISP Network)**
  - WAN: `172.16.0.x` (Assigned via DHCP by ISP Router)
  - LAN: `10.0.0.1/24`
- **Wireless AP:** `10.0.0.2`
- **Range Extender:** `10.0.0.3`
- **Devices (Phones, Laptops) Connected to LAN via WiFi`

### **What We Will Add:**
- **VMLAN (Virtual Machine Network)**
  - Subnet: `10.0.1.0/24`
  - pfSense Gateway: `10.0.1.1`
  - Managed by pfSense
  - DHCP Server Enabled
  - Proxmox VM Bridge: `vmbr1`

This setup allows for isolation of virtual machines while maintaining control over their network access.

---

## **Step 1: Create a Virtual Network Bridge (vmbr1) in Proxmox**

1. **Login to Proxmox Web UI.**
2. **Go to:** `Datacenter → Node (Your Proxmox Host) → Network`.
3. Click **Create → Linux Bridge**.
4. Configure:
   - **Name:** `vmbr1`
   - **Autostart:** `Enabled`
   - **IPv4 Address:** Leave Empty (pfSense will handle routing)
   - **IPv4/CIDR:** Leave Empty
   - **Bridge Ports:** Leave Empty
5. Click **Create**, then **Apply Configuration**.

---

## **Step 2: Add a Virtual Network Interface for pfSense**

1. **Shutdown pfsense VM in Proxmox Web UI**.
2. **Go to → pfSense VM → Hardware**.
3. Click **Add → Network Device**.
4. Configure:
   - **Bridge:** `vmbr1`
   - **Model:** `VirtIO` (Recommended for performance)
   - **VLAN Tag:** Leave Empty
5. Click **Add**, then **Start pfSense** to detect the new NIC.

---

## **Step 3: Assign the New Interface in pfSense**

1. **Login to pfSense Web UI.**
2. **Go to:** `Interfaces → Assignments`.
3. In the **Available Network Ports** dropdown, select the new interface (e.g., `vtnet1`, `igb1`, or `vmx1`).
4. Click **Add**, then click on the newly added interface (e.g., `OPT1`).
5. Configure:
   - **Enable Interface:** ✅
   - **Name:** `VMLAN`
   - **IPv4 Configuration Type:** `Static IPv4`
   - **IPv4 Address:** `10.0.1.1/24`
   - **IPv6 Configuration Type:** `None`
6. Click **Save & Apply Changes**.

---

## **Step 4: Enable DHCP for VMLAN**

1. **Go to:** `Services → DHCP Server → VMLAN`.
2. Enable DHCP and set:
   - **Range:** `10.0.1.100 - 10.0.1.200`
   - **Gateway:** `10.0.1.1`
3. Click **Save & Apply Changes**.

---

## **Step 5: Configure Firewall Rules for VMLAN**

Since pfSense blocks all traffic by default on new interfaces, we need to allow traffic on `VMLAN`.

1. **Go to:** `Firewall → Rules → VMLAN`.
2. Click **Add Rule**.
3. Set:
   - **Action:** `Pass`
   - **Interface:** `VMLAN`
   - **Protocol:** `Any`
   - **Source:** `10.0.1.0/24`
   - **Destination:** `Any`
   - **Description:** `Allow All Traffic from VMLAN`
4. Click **Save & Apply Changes**.

> **Optional:** If you want to isolate VMLAN from LAN (`10.0.0.0/24`), add a **Block Rule** above the Allow Rule with Destination `10.0.0.0/24`.

---

## **Step 6: Assign VMs to VMLAN**

1. **Go to Proxmox → VM → Hardware → Network**.
2. **Edit or add a new network device**, and select:
   - **Bridge:** `vmbr1`
   - **VLAN Tag:** Leave Empty
3. **Start the VM and configure networking:**
   - Set to **DHCP** (pfSense will assign an IP from `10.0.1.x`).
   - OR set manually (`10.0.1.50`, Subnet: `255.255.255.0`, Gateway: `10.0.1.1`).

---

## **Testing the Setup**
1. From a **VMLAN VM**, try:
   - `ping 10.0.1.1` (should work - testing gateway access).
   - `ping 10.0.0.1` (should work if not blocked).
   - `ping 1.1.1.1` (should work if internet access is enabled).

2. From a **LAN device (e.g., laptop, phone)**:
   - `ping 10.0.1.x` (should work if not blocked).
   - Try using `nslookup google.com` to test DNS resolution.

---

## **Conclusion**
You now have a dedicated **VMLAN (10.0.1.0/24) routed by pfSense**, managed separately from LAN. If needed, you can restrict access between these networks while still allowing shared services. This setup is flexible and scalable for future VLANs, additional firewall rules, or advanced routing.

🚀 **Next Steps:** Consider VLAN tagging for enhanced security, adding VPN access, or setting up separate subnets for different VM roles.

---
