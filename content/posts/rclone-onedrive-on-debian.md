---
title: "Rclone OneDrive on Debian"
date: 2023-12-11T02:30:00Z
description: "Seamlessly integrate your OneDrive with Debian"
categories: ["Linux Docs"]
tags: [linux, debian, rclone, onedrive]

cover:
  image: "/images/rclone.webp"
  alt: "Rclone OneDrive on Debian"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

Looking to seamlessly integrate your OneDrive with Debian? With the help of rclone, accessing and managing your OneDrive files becomes a breeze. This tutorial will walk you through the steps required to set up and mount your OneDrive storage on Debian. Say goodbye to cumbersome file transfers - let's streamline your cloud storage experience!


## To mount OneDrive in Debian using `rclone`, follow these steps:

1. **Create OneDrive Directory:**  
   Create a directory named `OneDrive` in your home directory.

   ```bash
   mkdir OneDrive
   ```

2. **Install rclone:**  
   Install `rclone` using the package manager.

   ```bash
   sudo apt install rclone
   ```

3. **Configure rclone:**  
   Run the configuration for `rclone`.

   ```bash
   rclone config
   ```

4. **Set Up OneDrive Remote:**  
   - Press `n` for a new remote.
   - Enter the name for the remote (e.g., "OneDrive").
   - Select the number corresponding to the remote type for OneDrive.
   - Authenticate using your Microsoft account:
     - No client ID or client secret is required.
     - Choose the Microsoft Cloud Global (1).
     - Advanced config editing can be skipped (n).
     - Use a web browser to authenticate (y).
     - Log in to your Microsoft account in the browser.
     - Choose OneDrive personal or business (1).
     - Select OneDrive type (personal) and confirm (y).
     - Confirm the found drive root type (y).
     - Keep this OneDrive remote (y).
   - Quit and close the configuration (q).

5. **Automatically Mount OneDrive on Login:**  
   Edit your `.profile` file 
   
   ```bash
   nano ~/.profile
   ```

   Append the following command at the end:

   ```bash
   /usr/bin/rclone --vfs-cache-mode writes mount OneDrive: ~/OneDrive &
   ```

   Save the file, log out, and log back in to automatically mount OneDrive upon login.

   Sometimes, the mount command might execute before the system is fully ready. Try adding a delay before mounting in the .profile file:

   ```bash
   (sleep 10 && /usr/bin/rclone --vfs-cache-mode writes mount OneDrive: ~/OneDrive &) 
   ```

Now, your OneDrive should be mounted automatically whenever you log in to your Debian system.

