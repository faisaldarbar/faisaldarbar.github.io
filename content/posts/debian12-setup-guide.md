---
title: "Debian 12 Setup Guide"
date: 2023-11-01T02:30:00Z
description: "A walk through and focus on security and essential configurations"
categories: ["Linux Docs"]
tags: [linux, debian, security, desktop, flatpak, ufw, firewall, clamav]

cover:
  image: "/images/debian12.webp"
  alt: "Debian 12 Setup Guide"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

In this blog post, I will walk you through the steps I took to set up my Debian 12 Desktop with a focus on security and essential configurations. I've also included resources that helped me along the way, and I'll give credit to those who deserve it. Let's get started!

## Credits

Before we dive into the setup, I want to acknowledge the following resources that greatly contributed to my knowledge and inspired this guide:

1. Learn Linux TV (Debian 12 - The First 12 Things You Should Do After Installation!) - <a href="https://youtu.be/K72XJHurdUY" target="_blank">Watch here</a>
2. NetworkChuck (5 Steps to Secure Linux (protect from hackers)) - <a href="https://youtu.be/ZhMw53Ud2tY" target="_blank">Watch here</a>
3. Chris Titus Tech (The Biggest Linux Security Mistakes) - <a href="https://youtu.be/QxNsyrftJ8I" target="_blank">Watch here</a>
4. ChatGPT - <a href="https://chat.openai.com" target="_blank">Access here</a> 

Now, let's explore the steps I took to set up my Debian 12 Desktop.

## Step 1: Adding a User with Sudo Privileges

The first thing you should do after a fresh Debian installation is to add a user with sudo privileges. Use the following command:

```bash
sudo usermod -aG sudo username
```
If you have setup Debian 12 you probably have a username apart from root. Replace username with your username. You will need to login as root to do this using the following command.

```bash
su -
```

## Step 2: Disabling the Root Password

To enhance security, it's recommended to disable the root password. This can be done using the following command:

```bash
passwd -l root
```

You will need to login as root to do the above using:

```bash
su -
```

## Step 3: Enabling the Root Password (If needed)

To enable the root password, use:

```bash
sudo passwd root
```

## Step 4: Installing Flatpak and Gnome Software Plugin

Flatpak is a package manager that allows you to easily install and run applications. You can install Flatpak and the Gnome Software Plugin using the following commands:

```bash
sudo apt install flatpak gnome-software-plugin-flatpak
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Step 5: Installing Browsers

Remove the default Firefox browser and install the latest version of Firefox through Gnome Software using the Flatpak version. 

```bash
sudo apt remove firefox-esr
```
Install Firefox through Gnome Software

Download Chrome via Firefox

You can install Chrome as follows with all dependencies:

```bash
sudo apt install ./google-chrome-filename.deb
```

Replace "google-chrome-filename.deb" with the actual file name.

You will need to be in the Downloads folder to do this.

## Step 6: Multimedia Codecs and VLC

To ensure you can enjoy multimedia content, install the necessary codecs and VLC media player:

```bash
sudo apt install libavcodec-extra vlc
```

## Step 7: Adding the "Backports" Repository

To access newer software packages, you can add the "Backports" repository. Create a file for it and add the repository with the following commands:

```bash
sudo nano /etc/apt/sources.list.d/backports.list
```

Add the following line to the file.

```bash
deb http://deb.debian.org/debian bookworm-backports main
```

## Step 8: Installing Uncomplicated Firewall (UFW)

For enhanced security, set up the Uncomplicated Firewall (UFW) and configure some basic rules to limit access:

```bash
sudo apt install ufw
```
Recommended UFW Rules

```bash
sudo ufw limit 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

## Step 9: Installing ClamAV and ClamTK

To keep your system free from malware, install ClamAV and its graphical front-end, ClamTK:

```bash
sudo apt install clamav clamtk
```

## Step 10: Customize GRUB Timeout

To tailor the boot menu timeout in Debian 12, follow these three commands:

Open a terminal and enter this command:

```bash
sudo nano /etc/default/grub
```

Modify the "GRUB_TIMEOUT" value to your desired seconds, e.g. 20 seconds:

```bash
GRUB_TIMEOUT=20
```

Save the file, update GRUB configuration:

```bash
sudo update-grub
```

Reboot your system, and the adjusted timeout will be reflected in the GRUB menu.

## Conclusion

With these steps, you've set up your Debian 12 Desktop with improved security and essential configurations. This guide is intended to help you and others looking to setup and secure their Debian environment. Feel free to refer back to this blog post as needed, and don't forget to explore the credited resources for more in-depth knowledge.

If you have any questions or further suggestions, please leave a comment below. Happy Linux-ing!
