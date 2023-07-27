---
layout: post
title: "My First Proxmox VE Install"
subtitle: "My First Proxmox Virtual Enviroment (PVE) Installation"
date: 2023-07-26 01:00:00 +0300
categories: [Proxmox]
tags: [proxmox, virtualization, hypervisor]
image: /images/my-first-proxmox-ve-install.jpg
---

## My First Proxmox Virtual Environment (PVE) Type 1 Hypervisor Installation

### **Objective**: 
Given the hardware and networking limitations in my homelab, I have opted to work with the available resources and set up my Proxmox server on a spare Laptop. The Server is connected to the network through Wi-Fi, and I have assigned a static IP address to ensure consistent connectivity. This will allow me to access the Proxmox web interface seamlessly from my main laptop.

The following steps were taken.

### **Identify Network Interface on Proxmox Server**:
Use `ip addr` or `ifconfig` to find the name of the Wi-Fi network interface (e.g., `wlan0`).

### **Create `wpa_supplicant`**:

To create the `wpa_supplicant.conf` file for Wi-Fi configuration, follow these steps:

   - Open a terminal on your Linux-based operating system.

   - Create the `wpa_supplicant.conf` file using a text editor, e.g., nano:

   ```plaintext
   sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
   ```

   - Add the following configuration details, replacing `Your_WiFi_SSID` with your Wi-Fi network's SSID and `Your_WiFi_Password` with the Wi-Fi password:

     ```plaintext
     ctrl_interface=/run/wpa_supplicant
     update_config=1

     network={
         ssid="Your_WiFi_SSID"
         psk="Your_WiFi_Password"
     }
     ```

     Example:

     ```plaintext
     ctrl_interface=/run/wpa_supplicant
     update_config=1

     network={
         ssid="MyWiFiNetwork"
         psk="MySecretPassword"
     }
     ```

   - Save and exit the file:
     - Press `Ctrl + O` to save the changes.
     - Press `Enter` to confirm the filename (`/etc/wpa_supplicant/wpa_supplicant.conf`).
     - Press `Ctrl + X` to exit `nano`.

   Please note that the `wpa_supplicant.conf` file's location may vary depending on your Linux distribution. If you encounter any issues with the file's location, refer to your distribution's documentation or community forums for the correct path.

### **Modify Network Configuration**:
- Open the network configuration file with a text editor:

   ```plaintext
   sudo nano /etc/network/interfaces
   ```

### **Set Static IP Address**:
  - Within the appropriate network interface section, add the following lines for static IP configuration:

   ```plaintext
   auto wlan0
   iface wlan0 inet static
       address <your_static_ip>
       netmask <your_netmask>
       gateway <your_gateway>
       dns-nameservers <dns_server_1> <dns_server_2>
       wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
   ```

   - Replace `<your_static_ip>`, `<your_netmask>`, `<your_gateway>`, `<dns_server_1>`, and `<dns_server_2>` with the desired network settings.


   - Save the changes and exit the text editor:
     - Press `Ctrl + O` to save the file.
     - Press `Enter` to confirm the filename.
     - Press `Ctrl + X` to exit `nano`.

### **Restart Networking Service**:
  - Apply the new network configuration:

   ```plaintext
   sudo systemctl restart networking
   ```

### **Start `wpa_supplicant`**:
  - Start the `wpa_supplicant` service to handle Wi-Fi authentication:

   ```plaintext
   sudo systemctl start wpa_supplicant
   ```

### **Verification**:
  - Check the new static IP address using `ip addr` or `ifconfig`:

   ```plaintext
   ip addr
   # or
   ifconfig
   ```

  - Ensure the static IP address is assigned to the Wi-Fi interface.

### **Access Proxmox Web Interface**:
  - Use the web browser on my laptop to access my Proxmox web server interface with the static IP address:

    ```plaintext
    https://<my_static_proxmox_server_ip>:8006/
    ```

  - Replace `<my_static_proxmox_server_ip>` with the actual static IP address I assigned to my Proxmox server.

### **Reboot and Automatic Reconnection**:
  - After reboot, the Proxmox server should automatically reconnect to the Wi-Fi network with the static IP configuration.

  - Verify that the Proxmox web interface is accessible after the reboot.

### **Troubleshooting**:

  - If issues are encountered with connectivity after a reboot, double-check the static IP configuration and Wi-Fi network availability.

  - If required change the static IP as and when necessary.

Note: For critical environments, consider using a wired Ethernet connection for greater reliability and stability. Ensure only using an unmanaged switch considering the current network's limitations.

### **Increasing Root Storage**:

*Credits to <a href="https://www.youtube.com/watch?v=_u8qTN3cCnQ" target="_blank">NetworkChuck</a> for the inspiration.*

In this section, I will guide you through the process of increasing root storage. 

If you go to your PVE node under the Datacenter and then go to summary in the left sidebar, you may notice that your HD space (root storage) does not correctly reflect the actual physical space on your drive. You can increase this by deleting `local-lvm` volume.

⚠️ **Important:** Only do this if you have additional physical disks on your server which you can use to create a ZFS pool and use it as a logical volume (LV) for the prospective VMs you plan to create. 

Then, follow these steps to effectively increase your root storage:

1. **Removing the `local-lvm` Volume:**
   Start by accessing the Proxmox GUI and navigating to the "Datacenter" section. Within the "Datacenter," proceed to the "Storage" tab and locate the "local-lvm" volume. Select it and choose the option to remove it. 

2. **Remove the Logical Volume via the PVE Shell:**
   Now, we'll switch to the PVE Shell. In the command line interface, execute the following command:
   ```bash
   lvremove /dev/pve/data
   ```

3. **Resizing the File System:**
   After removing the Logical Volume, it's time to resize the root storage. Run the following command in the PVE Shell:
   ```bash
   lvresize -l +100%FREE /dev/pve/root
   ```

4. **Resizing the Filesystem:**
   The next step is to resize the filesystem to make use of the additional space. Execute the following command:
   ```bash
   resize2fs /dev/mapper/pve-root
   ```

5. **Verifying Storage Changes:**
   Once the process is complete, return to the storage section in the Proxmox GUI. Confirm that the "local-lvm" volume is no longer present, and the root storage should now reflect a substantial increase in available space.

By following these steps, you have successfully increased the storage capacity of your Proxmox VE installation. Always exercise caution when making changes to storage configurations, and it's advisable to take backups before proceeding with any critical changes. Enjoy the enhanced storage capabilities in your Proxmox environment!