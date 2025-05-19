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
```
## 🐳 Step 3: Install Docker and Docker Compose

1. Create Install Script

```bash
nano install-docker.sh
```
2. Paste the Following into the Script

```bash
#Update Package Index and Install Prerequisites
echo "Updating package index and installing prerequisites..."
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

#Add Docker’s Official GPG Key and Repository
echo "Adding Docker's official GPG key and setting up repository..."
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

#Install Docker Engine
echo "Installing Docker Engine..."
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin

#Add Current User to the Docker Group
echo "Adding current user to the Docker group..."
sudo usermod -aG docker $USER

#Install Docker Compose
echo "Installing Docker Compose..."
DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/')
sudo curl -L "https://github.com/docker/compose/releases/download/$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

#Verify Installation
echo "Verifying Docker installation..."
docker --version
echo "Verifying Docker Compose installation..."
docker-compose --version

#Final Message
echo "Docker and Docker Compose installation completed successfully!"
echo "You need to log out and back in for group changes to take effect."
```


