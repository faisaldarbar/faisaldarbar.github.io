---
title: "Ubuntu Server Bare Metal - TTY Console Power Saving"
date: 2025-08-06T02:30:00Z
description: "I configured my headless Ubuntu Server on a Huawei Laptop to blank and power down the screen automatically when idle, even though it doesn't run a graphical environment."
categories: ["Homelab Docs"]
tags: [linux,tty,console,screen blanking,power management,ubuntu,headless server,rc.local,systemd,setterm,terminal,powerdown,shell script]


cover:
  image: "/images/ubuntu-server-bare-metal-tty-console-power-saving.webp"
  alt: "Ubuntu Server Bare Metal - TTY Console Power Saving"
  caption: "Photo by Alex Knight on Unsplash"

---

This appraoch helps conserve power and prevents OLED/LCD burn-in for an idle display.

---

### 🔧 Goal

Blank the screen after **1 minute**, then **power down the display after 2 minutes** — without affecting any background services like Samba or SSH.

---

### 🛠️ What I Did

1. **Created the rc.local file** (not available by default in newer Ubuntu versions):

   ```bash
   sudo nano /etc/rc.local
   ```

2. **Added this line to power-manage the console:**

   ```bash
   #!/bin/bash
   setterm --blank 1 --powerdown 2 < /dev/tty1
   exit 0
   ```

3. **Made it executable:**

   ```bash
   sudo chmod +x /etc/rc.local
   ```

4. **Created a systemd unit to support rc.local:**

   ```bash
   sudo nano /etc/systemd/system/rc-local.service
   ```

   Paste:

   ```ini
   [Unit]
   Description=/etc/rc.local Compatibility
   ConditionPathExists=/etc/rc.local
   After=network.target

   [Service]
   Type=forking
   ExecStart=/etc/rc.local start
   TimeoutSec=0
   RemainAfterExit=yes
   GuessMainPID=no

   [Install]
   WantedBy=multi-user.target
   ```

5. **Enabled the service:**

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable rc-local
   sudo systemctl start rc-local
   ```

6. **Verified it’s working:**

   ```bash
   journalctl -u rc-local.service --no-pager
   ```

---

### 🧪 Observations

* The screen **goes blank after 1 minute**, powers off after 2.
* It resumes **only on keyboard input**, not trackpad (expected on TTY).
* **SSH, Samba, and all services stay unaffected** — only the local screen powers down.
* Confirmed `consoleblank` value:

  ```bash
  cat /sys/module/kernel/parameters/consoleblank
  ```

---

### ✅ Final Thoughts

This is a clean, GUI-free solution for reducing display wear and power usage on servers with local screens. Great for headless setups, especially laptops, or servers with rarely-used displays.