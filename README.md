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

1. **Create a VM** (in Proxmox or other hypervisor): I used Vmware VM
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
3. Run the Script

```bash
chmod +x install-docker.sh
./install-docker.sh
exit
```
## 🌐 Step 4: Download and Install T-Pot
1. Install Git and Clone the Repository

```bash
sudo apt install git -y
git clone https://github.com/telekom-security/tpotce
cd tpotce
```
2. Start the Installation

```bash
sudo ./install.sh
```
During the installation:

Choose installation type Standard

Set a strong password for the user

The script will:

Pull all required Docker images

Set up honeypots

Configure Kibana dashboards

🕒 This step can take 10–45 minutes depending on your hardware and internet speed.

🚀 Step 5: Access the Web Interface
Once the installation finishes and the VM reboots:

Open your browser and go to:

```bash
https://<your_vm_ip>:64297
```
Log in with:

Username: The one you chose

Password: The one you set during installation

🧱 Optional: Setup Considerations
🔒 Network Isolation
Place the honeypot in a VLAN or DMZ

Use firewalls to block access from the internal network

📈 Log Management
T-Pot includes the ELK Stack:

Elasticsearch – stores logs

Logstash – processes logs

Kibana – visualizes logs

You can also forward logs to a centralized SIEM.

🪵 Disk Usage Tips
T-Pot logs grow fast; configure log rotation

Consider adding an external HDD or expanding the VM disk

🔁 Step 6: Post-Install Tips
✅ Check Docker Containers

```bash
docker ps
```
📄 Monitor Docker Logs
see containers
```bash
journalctl -u docker.service
```
📊 Access Kibana Dashboard
Open:

```bash
https://<your_vm_ip>:64297
```
use credentials when prompted

## 📊 T-Pot Dashboard: Kibana & ELK Stack

Once you log in at `https://<your_vm_ip>:64297`, you’ll see the T-Pot Kibana dashboard:

### 🖥️ Kibana Main Dashboard

![Kibana Main Dashboard](images/kibana.PNG)

---

### 📂 Elastic Stack Log View

You can dig into captured logs using the Kibana interface:

![Elastic Logs View](images/elastic-idle.PNG)

---

## 🔍 Zenmap/Nmap Scan Results

After installing T-Pot, I used **Zenmap (Nmap GUI)** to scan the VM’s IP to simulate attacker behavior.

![Zenmap Nmap Results](images/nmap-tpotserver-ip.PNG)

These actions triggered events visible in Elasticsearch, showing how T-Pot logs and detects network scans.

## 🔍 Elastic Stack: View Logs After Nmap Scan

After running an Nmap scan on the T-Pot VM IP (using Zenmap or command line), the scan activity is captured and logged by T-Pot’s honeypot services. These logs are visible in the Elasticsearch backend via Kibana.

Below is a screenshot of the **Elastic Logs view** showing the Nmap scan events captured:

![Elastic Logs View After Nmap Scan](images/after-nmap.png)

You can see details such as:

- Source IP address of the scan
- Timestamps of the scan events
- Protocols and ports targeted
- Event severity and types

This visualization helps monitor and analyze attacker behavior on your honeypot.

---

## 🚀 Wrapping It Up: Your Honeypot Project in a Nutshell

You’ve transformed a simple Ubuntu VM into a powerful honeypot platform with T-Pot, Elasticsearch, and Kibana—turning your setup into an effective tool for capturing and analyzing attacker behavior. With Zenmap scans triggering real-time logs, you now have a hands-on way to understand network threats and improve your security skills.

This project combines practical tools and real data to give you a solid foundation in cybersecurity monitoring. Keep exploring, tweaking, and learning—your honeypot is ready to catch whatever comes next.

Happy hunting!






