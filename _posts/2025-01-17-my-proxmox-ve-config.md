---
layout: post
title: "Proxmox VE Configuration"
subtitle: "My Proxmox VE Installation and Secure Configuration Guide"
date: 2025-01-17 01:00:00 +0530
categories: [Proxmox]
tags: [proxmox, hypervisor, virtualization]
image: /images/proxmox-host.png
---



# My Proxmox VE Installation and Secure Configuration Guide

## Introduction

No need to back up your Proxmox host. Always do a fresh install and fresh configuration using this documentation, as it provides a cleaner option for setup. It's crucial to document everything for future reference, ensuring consistency and security with each installation.

---

### **1. Install Proxmox VE**

To begin, you'll need to install Proxmox VE on your host. Proxmox is a powerful, open-source virtualization platform that allows you to manage virtual machines and containers.

#### **Installation Steps:**

1. **Download the ISO**:
   - Visit the Proxmox VE download page to download the latest ISO for Proxmox VE.

2. **Create a Bootable USB**:
   - I encountered issues when trying to use **Ventoy** to install Proxmox. The installer couldn't find the ISO correctly. So, I used **Rufus** to create the bootable USB.
   - Download and install **Rufus**, and create a bootable USB stick using the Proxmox ISO.

3. **Install Proxmox**:
   - Boot the server from the USB stick.
   - Follow the installation wizard, and when asked, select **ZFS** as the file system for your installation.
   - Make sure to configure the **IP address** and **DNS settings** correctly during installation to match your network setup.

4. **Complete the Installation**:
   - Once the installation is complete, the system will prompt you to remove the installation media.
   - Reboot the server and log into the Proxmox web interface.

---

### **2. Accessing Proxmox Web Interface**

After installation, you can manage Proxmox via a web interface:

1. Open a browser and go to:
   ```plaintext
   https://<Proxmox-IP>:8006
   ```

2. Log in using the `root` username and the password you set during installation.

---

### **3. Install `sudo` and Add a Non-Root User**

Before creating a non-root user, install the **`sudo`** package, as it may not be installed by default.

#### **Install `sudo`:**

1. Install `sudo` using the following command:
   ```bash
   apt update
   apt install sudo
   ```

2. After installing `sudo`, proceed to create a new user.

#### **Create a New Non-Root User:**

1. Create a new user by replacing `username` with your preferred username:
   ```bash
   adduser username
   ```

2. Add the user to the `sudo` group to grant administrative privileges:
   ```bash
   usermod -aG sudo username
   ```

3. Verify that the user is part of the `sudo` group:
   ```bash
   groups username
   ```

---

### **4. Disable Root Access via SSH**

For security, it is recommended to disable `root` access via SSH.

#### **Steps to Disable Root SSH Login:**

1. Edit the SSH configuration file:
   ```bash
   nano /etc/ssh/sshd_config
   ```

2. Find the line containing `PermitRootLogin` and change it to `no`:
   ```plaintext
   PermitRootLogin no
   ```

3. Restart the SSH service to apply the change:
   ```bash
   systemctl restart sshd
   ```

---

### **5. Disable Root Access via the Proxmox GUI**

To enhance security, it's also recommended to disable the `root` user from accessing the Proxmox web interface.

1. **Login to Proxmox Web Interface** using the `root` account.

2. In the Proxmox Web UI, go to **Datacenter** > **Permissions** > **User**.

3. Locate the `root` user and click **Edit**.

4. Uncheck the **Enable** box to disable the root account.

5. Click **Save** to apply the changes.

---

### **6. Enable Two-Factor Authentication (2FA) for SSH**

To further secure your Proxmox host, you can enable Two-Factor Authentication (2FA) for SSH logins. 

#### **Install Google Authenticator PAM Module:**

1. Install the Google Authenticator PAM module:
   ```bash
   apt install libpam-google-authenticator
   ```

2. Configure SSH to use the Google Authenticator PAM module:
   - Edit the SSH configuration file:
     ```bash
     nano /etc/ssh/sshd_config
     ```
   - Ensure the following lines are present:
     ```plaintext
     ChallengeResponseAuthentication yes
     UsePAM yes
     ```

3. Edit the PAM configuration to include Google Authenticator:
   - Edit the PAM SSH configuration file:
     ```bash
     nano /etc/pam.d/sshd
     ```
   - Add the following line at the top:
     ```plaintext
     # SSH to Use Google Authenticator
     auth required pam_google_authenticator.so
     ```

4. Restart SSH to apply the changes:
   ```bash
   systemctl restart sshd
   ```

---

### **7. Configure Google Authenticator**

Now that you’ve enabled 2FA, configure Google Authenticator for your user.

1. Log in as your user (`username`):
   ```bash
   su - username
   ```

2. Run the following command to set up Google Authenticator:
   ```bash
   google-authenticator
   ```

3. Follow the prompts to:
   - Scan the QR code with the Google Authenticator app on your phone.
   - Save the emergency scratch codes in case you lose access to the app.

---

### **8. Test SSH Access with 2FA**

To ensure 2FA is working correctly, test logging in via SSH from another terminal or machine:

```bash
ssh username@<Proxmox-IP>
```

- After entering your password, you'll be prompted to enter a verification code generated by the Google Authenticator app.

---

That's all.

