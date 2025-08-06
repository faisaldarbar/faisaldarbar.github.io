---
title: "📌 Ubuntu Server (fd-huawei) Planned Role"
weight: 2
date: 2025-08-04T03:30:00Z
description: "A multi-role utility planning and service map for this Ubuntu-based bare metal support node in my self-hosted infrastructure."
categories: ["Homelab Docs"]
tags: [homelab, ubuntu server, self-hosted, samba, wazuh, sysmon, fail2ban, suricata, zeek, ansible, terraform, pentesting, tailscale, netdata, devops, internal tools, PVE support, lightweight server]

cover:
  image: "/images/ubuntu-server-virtual-machine.webp"
  alt: "Ubuntu Server (fd-huawei) Planned Role"
  caption: "Photo by Florian Krumm on Unsplash"

---

*Internal reference for personal homelab planning and service mapping*

This post outlines how I’ve repurposed my **Huawei laptop (Ubuntu Server, 8GB RAM, 240GB SSD)** as a **multi-role utility and support node** for my self-hosted infrastructure, primarily managed via **Proxmox VE (PVE)**.

Rather than just making it a Samba file share, this setup is curated to:

* Support my development VMs and infra running on PVE
* Act as a lightweight **security**, **monitoring**, and **automation node**
* Enable **safe learning environments** for tools like Wazuh, Ansible, and Suricata
* Avoid running unnecessary GUI or redundant services

---

## 🎯 Primary Roles of the Huawei Server

This is the **finalized shortlist** of tools/services I chose to run on the Huawei server, categorized by purpose, whether they support my PVE/dev infrastructure, and their description.

| Tool / Service              | Purpose                                           | Infra Support  | Description                                                                                                                                           |
| --------------------------- | ------------------------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Samba**                   | Central file storage / digital assets             | ✅ Yes          | Exposes a shared drive for media, backups, and app files. Accessible by VMs on PVE and other machines.                                                |
| **Wazuh AIO**               | Security SIEM & monitoring                        | ✅ Yes          | Collects logs, detects anomalies, and shows dashboards. Agents can be installed on other PVE VMs (like dev-ubuntu, PBS). Great for security practice. |
| **Auditd + Sysmon (Linux)** | File, process, and syscall logging                | ✅ Yes          | Helps track exactly what’s happening on the system — who accessed what, when. Useful for hardening or forensic exercises.                             |
| **Fail2Ban**                | SSH brute-force defense                           | ✅ Yes (Huawei) | Monitors logs for failed login attempts and bans IPs via firewall rules. Simple and effective.                                                        |
| **Suricata** or **Zeek**    | Passive network intrusion detection               | ✅ Yes          | Can detect port scans, suspicious payloads, and LAN anomalies. If configured well, it sees traffic from across VLANs/subnets.                         |
| **Ansible / Terraform**     | Infrastructure automation & provisioning          | ✅ Yes          | Acts as my control node for setting up and managing VMs, pushing configs, and testing automation workflows. Great for DevOps learning.                |
| **Pentest Tools**           | Safe experimentation with security scanning tools | 🧠 Learning    | Test tools like Nmap, Nikto, OpenVAS, Metasploit in a safe LAN lab without harming production VMs.                                                    |
| **Tailscale**               | Secure remote access                              | ✅ Yes          | Allows encrypted access to Huawei (or PVE VMs) from my phone or laptop from anywhere — no router config needed.                                       |
| **Netdata (optional)**      | Real-time system dashboard                        | 💡 Local       | Shows CPU, RAM, disk I/O, Samba usage. Very lightweight. Can be skipped if Wazuh is sufficient.                                                       |

---

## 🧩 Additional Notes & Rationale

* 🧠 **Why not a GUI?**

  * I avoid running a desktop environment unless absolutely necessary — this keeps RAM usage low (important with only 8GB available).
  * If needed temporarily, I can install `Xfce` and launch it via `startx`.

* 🛡️ **Why Wazuh instead of only Netdata?**

  * Wazuh gives a **complete security dashboard**, audit reports, and alerting. It can monitor the Huawei **and** other nodes with agents.
  * Netdata is real-time and beautiful, but more for metrics than full log analytics or SIEM.

* 🌐 **Why Suricata or Zeek?**

  * These allow me to observe traffic **without putting agents on every VM**. Especially useful if I mirror traffic to the Huawei's NIC.

* ⚙️ **Why use Ansible?**

  * With multiple VMs in Proxmox (dev-ubuntu, coolify, PBS), automation becomes valuable.
  * I can use Ansible to push packages, configs, and files to all nodes at once, making future expansion simpler.

* 🔐 **Why Tailscale?**

  * It simplifies secure, encrypted remote access to this node (and others) without dealing with router settings or VPNs.

---

## 🔄 Future Considerations

These are not urgent, but worth exploring as the homelab evolves:

| Tool                       | Reason to Consider                                             |
| -------------------------- | -------------------------------------------------------------- |
| **Podman/Docker**          | Might containerize security tools or automation agents         |
| **VS Code Server**         | Only if I want a browser-based editor on this node             |
| **Syslog Relay**           | Use Huawei as a central log collector for lightweight devices  |
| **Backups of Dev Configs** | Huawei could rsync or snapshot Samba + configs to PBS or cloud |

---

## 📌 Conclusion

The Huawei server is no longer “just a Samba box.” It now acts as a:

* **Security visibility node**
* **Automation command center**
* **Real-time monitoring point**
* **LAN-aware passive analyzer**
* **Learning platform for security and DevOps**

This setup is efficient, resource-conscious, and helps me grow into advanced infrastructure practices without wasting the device’s potential.

