# 🛡️ T-Pot Honeypot Installation Guide (Ubuntu Server 24.04 LTS)

**T-Pot** is a comprehensive honeypot platform that integrates multiple honeypot tools with the ELK Stack (Elasticsearch, Logstash, Kibana) for powerful threat analysis and visualization.

This guide installs T-Pot in a **Proxmox VM**, but it can be adapted for other environments or physical machines.

---

## 🧰 System Requirements

**Minimum Recommended Specs:**

- **OS:** Ubuntu Server 24.04 LTS (Minimal Install)
- **RAM:** 8–16 GB
- **Disk:** 128+ GB (T-Pot logs consume space quickly)
- **CPU:** 2+ cores
- **Network:** Unfiltered internet access (DMZ/VLAN recommended)

---

## 🪟 Step 1: Prepare the Ubuntu VM

1. **Create a VM** (in Proxmox or other hypervisor):
   - **OS:** Ubuntu Server 24.04 LTS (64-bit)
   - **RAM:** 8 GB or more
   - **CPU:** 2 cores or more
   - **Disk:** At least 128 GB

2. During Ubuntu setup:
   - Select **Minimal Installation**
   - Enable **OpenSSH Server** for remote access

---

## 🔁 Step 2: Update the System

SSH into the VM or use the Proxmox console and run:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y
