---
title: "Proxmox Backup Server"
date: 2025-01-31T02:30:00Z
description: "My Proxmox Backup Server Installation and Secure Configuration Guide"
categories: ["Homelab Docs"]
tags: [proxmox, backup, server]

cover:
  image: "/images/proxmox-backup-server.webp"
  alt: "Proxmox Backup Server"
  caption: "Photo by Scott Rodgerson on Unsplash"

---

Just like with Proxmox VE, I prefer a fresh installation and configuration of Proxmox Backup Server (PBS) for a clean and consistent setup. While I would back it up if possible, PBS requires that the machine be either stopped or suspended for backup, meaning it may not be able to back itself up. However, I’ve heard that one could have another instance of PBS to back up the first.  

Given this, documenting the entire process ensures reproducibility, security, and stability for each installation.  

---  

## **1. Installing Proxmox Backup Server (PBS) as a VM on Proxmox VE**  

PBS is a dedicated backup solution for Proxmox environments, offering efficient and secure data storage. In this setup, PBS is installed as a virtual machine (VM) on Proxmox VE.  

### **Installation Steps:**  

1. **Download the ISO**:  
   - Visit the Proxmox Backup Server download page and download the latest ISO.  

2. **Create a New Virtual Machine (VM) in Proxmox VE:**  

   - **General:**  
     - **Name:** pbs 

   - **OS:**  
     - **ISO Image:** Select PBS ISO  

   - **System:**  
     - **Machine:** Q35  
     - **BIOS:** UEFI  
     - **Add EFI Disk:** Checked  
     - **EFI Storage:** local-lvm
     - **Pre-Enroll Keys:** Unchecked
     - **QEMU Agent:** Checked  
     - **SCSI Controller:** VirtIO SCSI Single  

   - **Disks:**  
     - **VirtIO0 (OS):**  
       - **Bus/Device:** VirtIO Block  
       - **Storage:** local-lvm  
       - **Disk Size:** 16GB  
       - **Discard:** Enabled  
     - **SCSI0 (Datastore):**  
       - **Bus/Device:** SCSI  
       - **Storage:** local-lvm  
       - **Disk Size:** 104GB  
       - **Discard:** Enabled  

   - **CPU:**  
     - **Cores:** 2  
     - **Type:** Host  

   - **Memory:**  
     - **Memory:** 4GB - Note: Running Proxmox Backup Server (PBS) with **2 GB RAM** is not recommended due to potential performance issues, slow backups, and increased swap usage. For stability and smooth operation, especially when handling deduplication and indexing, it's best to allocate **4 GB RAM**. Since the VM is only used occasionally and turned off after use, keeping it at **4 GB** ensures reliable performance without unnecessary slowdowns.
     - **Ballooning:** Checked  

   - **Network:**  
     - **Bridge:** vmbr0  
     - **Model:** VirtIO (paravirtualized)  
     - **Firewall:** Checked (Optional - Enable if you need additional network security based on your network setup)  

   - **Confirm:**  
     - Review settings and complete VM creation  

3. **Install PBS**:  
   - Start the VM and proceed with the installation.  
   - Follow the installation wizard and select **ext4** as the filesystem (default).  
   - Set the **FQDN** hostname in the format of your choice. Mine is `hostname.yourdomainname.com`.  
   - Configure the **IP address** and **DNS settings** according to your network setup.  

4. **Complete the Installation**:  
   - Reboot the system and access the PBS web interface.
   - Stop the VM and remove the installation media.
   - Preferably delete the ISO from local (pve) to save space on your disk.
   - Ensure the **boot order** is set correctly in VM options, with the correct virtual disk as the primary boot device.

---  

## **2. Accessing the PBS Web Interface**  

After installation, PBS is managed via a web interface.  

1. Open a browser and navigate to:  
   ```plaintext
   https://<PBS-IP>:8007
   ```
2. Log in using the `root` user and the password set during installation.  

---  

## **3. Configure Repositories**  

Proxmox Backup Server offers enterprise repositories by default, but for non-subscribed users, you should switch to the **pbs-no-subscription** repository to ensure access to the necessary updates without subscription fees.

#### **Enable the pve-no-subscription Repository:**

1. **Use the GUI:**  
   - Navigate to **Datacenter > PBS-VM > Updates > Repositories**.  
   - Disable the **enterprise repository**.  
   - Add a new repository for **pbs-no-subscription**.

2. **Verify via Terminal:**  
   - Open the sources list:
     ```bash
     nano /etc/apt/sources.list
     ```

3. **Ensure the file contains the following:**  
   ```plaintext
   deb http://ftp.debian.org/debian bookworm main contrib

   deb http://ftp.debian.org/debian bookworm-updates main contrib

   # security updates
   deb http://security.debian.org bookworm-security main contrib

   deb http://download.proxmox.com/debian/pbs bookworm pbs-no-subscription
   ```

4. **Save and Close:**  
   - Press `Ctrl+O` to save, `Enter` to confirm, and `Ctrl+X` to exit.

5. **Update the System:**  
   ```bash
   apt update && apt upgrade -y
   ```

---  

## **4. Root User Access in PBS**  

Unlike Proxmox VE, using the `root` user in PBS is necessary for full functionality. Disabling `root` login can cause issues such as:  
- Loss of access to the built-in PBS shell in the web interface.  
- Datastore permission problems.  

Additionally, there is no option in the PBS GUI to create a fully functional PAM user—only PBS-specific users with admin rights.  

For these reasons, **root access should remain enabled** in both SSH and the PBS web interface. However, you can **secure the root user with 2FA**.  

---  

## **5. Enable Two-Factor Authentication (2FA) for SSH and GUI**  

To enhance security, enabling Two-Factor Authentication (2FA) is highly recommended for both SSH and the PBS web GUI.  

### **Enable 2FA for SSH using Google Authenticator**  

1. Install the module:  
   ```bash
   apt install libpam-google-authenticator
   ```  
2. Edit the SSH configuration:  
   ```bash
   nano /etc/ssh/sshd_config
   ```  
   Ensure these lines are present:  
   ```plaintext
   KbdInteractiveAuthentication yes
   ChallengeResponseAuthentication yes
   UsePAM yes
   ```  
3. Modify the PAM SSH configuration:  
   ```bash
   nano /etc/pam.d/sshd
   ```  
   Add this line at the bottom:  
   ```plaintext
   # PAM SSH configuration for Google Authenticator
   auth required pam_google_authenticator.so
   ```  
4. Restart SSH:  
   ```bash
   systemctl restart sshd
   ```  

### **Configure Google Authenticator**

Now that you’ve enabled 2FA, configure Google Authenticator for your user.

1. Run the following command to set up Google Authenticator:
   ```bash
   google-authenticator
   ```

2. Follow the prompts to:
   - Scan the QR code with the Google Authenticator app on your phone.
   - Save the emergency scratch codes in case you lose access to the app.

### **Enable 2FA for the PBS Web GUI**  

- Navigate to **Configuration > Access Control > Users**.  
- Go to the **2FA tab** and enable Two-Factor Authentication.  
- Follow the steps to set up Google Authenticator, similar to the SSH setup.  

---  

## **6. Linking PBS to Proxmox VE (PVE) for Backup**  

Before linking PBS with Proxmox VE, you need to **create a directory in the storage disks and configure it as a datastore**.  

### **Steps to Link PBS with Proxmox VE**  

1. **Create a Datastore**:  
   - On your PBS server, go to **Storage > Directory** and create a directory.  
   - Select the unused disk and format it as **ext4**.  
   - Name it and check the box to **add it as a datastore**.  

2. **Add PBS as a Storage Repository in Proxmox VE**:  
   - In the Proxmox VE web interface, navigate to **Datacenter > Storage**.  
   - Click **Add** and select **Proxmox Backup Server**.  
   - Enter:  
     - **Storage ID** (can be the same as your PBS hostname).  
     - **PBS IP address** as the server.  
     - **PBS root username and password**.  
     - **Datastore name** (e.g., `backup`).  
   - Copy the **Fingerprint** from the PBS web interface dashboard and paste it in Proxmox VE.  

---  

## **7. Manually Backing Up the pfSense VM**  

1. **Manually STOP the pfSense VM from the pfSense console.**
2. **In Proxmox VE:**  
   - Select the stopped **pfSense VM**.  
   - Click **Backup**, then select **Backup Now**.  
   - Choose the PBS storage repository created earlier.  
   - Select **Stop** as the backup mode.  
3. Click **Backup** to initiate the process.   

---  

## **8. Monitoring Backup Status**  

To verify your backups, go to **PBS > Datastore > Backup Datastore** and check for the latest backup entries.  

---  

## **Conclusion**  

This guide covers the essential steps to install, secure, and configure PBS as a VM on Proxmox VE. In future posts, we’ll explore **advanced PBS features**. 🚀 

For the official PBS Documentation got to the PBS GUI and click on Documentation in the top right corner.

