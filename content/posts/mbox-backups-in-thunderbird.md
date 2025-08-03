---
title: "MBOX Backups in Thunderbird"
date: 2025-02-27T02:30:00Z
description: "A Complete Guide to MBOX Backups in Thunderbird"
categories: ["FOSS Docs"]
tags: [mbox, thunderbird, backups]

cover:
  image: "/images/mbox-backups-in-thunderbird.webp"
  alt: "MBOX Backups in Thunderbird"
  caption: "Photo by Martin Robles on Unsplash"

---

Backing up your emails is essential, especially when using IMAP-based email services like Microsoft 365 (M365) and Zoho Mail. Since M365 does not provide access to emails once your subscription ends, and Zoho Mail may have similar restrictions, MBOX backups are a reliable way to store and access your emails offline.

### This guide covers:
- Why MBOX is the best backup option  
- How to manually back up MBOX files in Thunderbird  
- How to restore MBOX backups  
- Automating and securing your backups  

## Why Use MBOX for Email Backups?
MBOX is a universal email storage format supported by Thunderbird and many other email clients. Here’s why it’s useful:

### Benefits of MBOX Backups:
- **Full Backup** – Stores emails with attachments inside a single file.  
- **No Subscription Lock-in** – Even if your Microsoft 365 or Zoho Mail account expires, your emails remain accessible.  
- **Easily Restorable** – Can be imported back into Thunderbird or other MBOX-compatible email clients.  
- **Portable** – Can be stored in OneDrive, Google Drive, or an external drive for extra security.  

## How to Manually Back Up Emails as MBOX in Thunderbird
Thunderbird **already stores emails in MBOX format** without needing an extension. You just need to copy the MBOX files from your profile folder.

### Step 1: Locate Your Thunderbird Profile Folder
1. Open **Thunderbird**
2. Click **☰ Menu > Help > More Troubleshooting Information**
3. Under **"Application Basics,"** find **"Profile Folder"**
4. Click **"Open Folder"** (this opens your Thunderbird profile directory)

### Step 2: Back Up MBOX Files
1. **Close Thunderbird** to avoid file access conflicts.
2. Navigate to the following folder based on your account type:
   - **For IMAP Accounts:**
     ```
     ImapMail\[your mail server]\
     ```
   - **For POP Accounts:**
     ```
     Mail\Local Folders\
     ```
3. Find the **MBOX files** (they have the same name as your email folders, with **no file extension**).
4. **Copy and paste** these files to a backup location (External Drive, Cloud Storage, etc.).

📌 **Tip:** Do NOT back up `.msf` or `wdseml` files. They are just index files and will be rebuilt by Thunderbird if needed.

## How to Restore MBOX Emails in Thunderbird
If you need to restore your backup, follow these steps:

1. **Close Thunderbird**
2. Copy your backed-up **MBOX files** back into the same location:
   - For IMAP: `ImapMail\[your mail server]\`
   - For POP: `Mail\Local Folders\`
3. Restart **Thunderbird** – your emails should appear automatically under the correct folders.

## Automating & Securing MBOX Backups
Since MBOX backups are manual, you can set up a system to back up your emails regularly.

### Best Practices:
- **Set a Reminder** – Use Google Calendar or a Task Manager to remind you to back up once a month.
- **Use Cloud Storage** – Save backups to OneDrive, Google Drive, or Dropbox.
- **Keep Multiple Copies** – Store backups on an external hard drive for extra security.

## Conclusion
Using MBOX backups ensures that your emails remain safe, even if your email provider locks you out. Since Thunderbird **natively stores emails in MBOX format**, you don't need any extensions—just manually copy the files for backup.

By following this guide, you can securely store your emails, prevent data loss, and keep your important business communications accessible at all times.

🚀 **Stay backed up, stay secure!**

