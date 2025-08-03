---
title: "Ubuntu Docker Portainer Setup Guide"
date: 2025-02-06T02:30:00Z
description: "My Ubuntu Server Docker Portainer Installation and Secure Configuration Guide"
categories: ["Homelab Docs"]
tags: [ubuntu, docker, portainer]

cover:
  image: "/images/ubuntu-docker-portainer.webp"
  alt: "Ubuntu Docker Portainer Setup Guide"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

In this comprehensive guide, we'll walk through the steps to set up an Ubuntu Server VM, configure networking with pfSense, and install Docker and Portainer for container management. We'll also highlight issues encountered during the setup process and how they were resolved.

---

## 1. Creating the VM in Proxmox

### VM Configuration:

**General:**  
- **Name:** docker  

**OS:**  
- **ISO Image:** Select Ubuntu Server ISO  

**System:**  
- **Machine:** Q35  
- **BIOS:** UEFI  
- **Add EFI Disk:** Checked  
- **EFI Storage:** local-lvm  
- **Pre-Enroll Keys:** Unchecked  
- **QEMU Agent:** Checked  
- **SCSI Controller:** VirtIO SCSI Single  

**Disks:**  
- **VirtIO0 (OS Disk):**  
  - **Bus/Device:** VirtIO Block  
  - **Storage:** local-lvm  
  - **Disk Size:** 64GB *(Future-proofed for OS, updates, and Docker overhead)*  
  - **Discard:** Enabled *(Allows TRIM for SSD lifespan and performance)*  

- **SCSI0 (Persistent Storage):**  
  - **Bus/Device:** SCSI  
  - **Storage:** local-lvm  
  - **Disk Size:** 100GB *(For Docker volumes, databases, and additional services)*  
  - **Discard:** Enabled  

**CPU:**  
- **Cores:** 2 *(Starter config for basic services, can be increased later)*  
- **Type:** Host  

**Memory:**  
- **Memory:** 2GB *(Sufficient for a start, scalable later)*  
- **Ballooning:** Checked

**Network:**  
- **Bridge:** vmbr1 *(Connected to VLAN)*  
- **Model:** VirtIO (paravirtualized)  
- **Static IP:** Set *(Recommended for Pi-hole and Home Assistant)*  
- **Firewall:** Checked *(Managed via pfSense, but can add VM-level rules if needed)*  

**Confirm:**  
Review settings and complete VM creation. 🚀  


---

## 2. Network Configuration with pfSense

### Setting Up VMLAN:

1. **Interface Assignments:**
   - Go to **Interfaces > Assignments** in pfSense.
   - Add a new interface and assign it to the virtual bridge created in Proxmox VE (vmbr1).
   - Rename the interface to **VMLAN**.

2. **Configuring VMLAN:**
   - Go to **Interfaces > VMLAN**.
   - Enable the interface.
   - Set the **IPv4 Configuration Type** to **Static IPv4**.
   - Set the **IPv4 Address** to `10.0.1.1/24`.
   - Save and apply the settings.

3. **DHCP Server Configuration:**
   - Navigate to **Services > DHCP Server > VMLAN**.
   - Enable the DHCP server.
   - Set the DHCP range, e.g., `10.0.1.100` to `10.0.1.200`.
   - Save and apply the settings.

4. **Firewall Rules:**
   - Go to **Firewall > Rules > VMLAN**.
   - Add a new rule:
     - **Action:** Pass
     - **Protocol:** Any
     - **Source:** VMLAN net
     - **Destination:** Any
   - Save and apply the rule.

With these steps, the VM connected to vmbr1 will get an IP in the `10.0.1.x` range and have full network access.

---

## 3. Installing Ubuntu Server

1. **Boot the VM using the Ubuntu Server ISO**.
2. Proceed with the installation and select **"Use entire disk"** when prompted.
3. Install the **OpenSSH Server** when prompted.
4. Complete the installation and **reboot**.

### Issue Encountered:
- **Mirror URL Check Failure:**
  - During installation, the mirror URL check failed but we continued without issues.
- **CD-ROM Unmounting Error:**
  - On reboot, an error about unmounting `cdrom.mount` appeared, but it didn't affect the installation.

---

## 4. Setting Static IP Address

### Disabling Cloud-Init Network Configuration:

1. Create a file to disable cloud-init network settings:

```bash
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

Add the following content:

```yaml
network: {config: disabled}
```

2. Edit the Netplan configuration:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Replace with:

```yaml
network:
  version: 2
  ethernets:
    enp6s18:
      dhcp4: no
      addresses:
        - 10.0.1.2/24
      gateway4: 10.0.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

3. Apply the configuration:

```bash
sudo netplan apply
```

### Issue Encountered:
- **Deprecated `gateway4` Warning:**
  - Netplan displayed warnings about `gateway4` being deprecated but functioned correctly.
- **SSH Disconnection:**
  - Applying Netplan caused an SSH disconnection. Reconnected using the new static IP `10.0.1.2`.

---

## 5. Installing Docker

1. **Update packages:**

```bash
sudo apt update && sudo apt dist-upgrade -y
```

2. **Install Docker:**

```bash
sudo apt install -y docker.io
```

3. **Enable Docker to start on boot:**

```bash
sudo systemctl enable docker
```

4. **Test Docker installation:**

```bash
sudo docker run hello-world
```

5. **Add user to Docker group (optional):**

```bash
sudo usermod -aG docker $USER
```

Log out and back in to apply group changes.

---

## 6. Installing Portainer

1. **Pull and run Portainer with HTTPS:**

```bash
sudo docker run -d -p 9443:9443 --name=portainer --restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /opt/portainer/data:/data \
portainer/portainer-ce:latest --ssl
```

2. **Access Portainer GUI:**
   - Visit `https://10.0.1.2:9443` in your browser.
   - Set up the initial admin account.

### Issue Encountered:
- **Container Name Conflict:**
  - Received an error about the container name already being in use due to the previous container created using the http protocol. Resolved by removing the existing container:

```bash
sudo docker rm -f portainer
```

- **Self-Signed Certificate Warning:**
  - Portainer uses a self-signed SSL certificate by default, similar to Proxmox VE.

---

## 7. Troubleshooting and Issues Encountered

- **Proxmox Backup Size:**
  - Initial backups were only ~500 KB due to excluding the main VM disk (`backup=no` in VM disk settings).
  - To include the VM's disk in backups, ensure the backup option is enabled for the primary disk.

---

## 8. Backing Up the VM Using Proxmox Backup Server

1. **Configure the VM to include disks in backup:**
   - In Proxmox, go to the VM settings.
   - Edit the disk settings and enable the **backup** option.

2. **Manually do a backup job:**
   - Go to **VM > Backup**.
   - Manually do a backup targeting your Proxmox Backup Server (PBS).

---

## Conclusion

By following this guide, you've successfully set up an Ubuntu Server VM, configured networking with pfSense, installed Docker, and deployed Portainer for container management. With proper network configuration and troubleshooting, your system is now ready for scalable and secure containerized applications.
