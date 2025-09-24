+++ 
draft = false
date = 2025-09-12T19:12:58+01:00
title = "Mythic Server Setup Tutorial, Day 20 "
description = "SOC Analyst Challenge, Day 20 "
slug = "SOC Analyst Challenge, Day 20"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++


## Day 20: Mythic Server Setup Tutorial
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)
 
 
**Important Security Note:** This setup is for a **lab environment only**. Exposing Mythic to the internet without proper hardening and obfuscation (e.g., using domain fronting, custom C2 profiles) will lead to quick detection.


### **Mythic C2 Server Setup Tutorial**

#### **What is Mythic?**
Mythic is a sophisticated, cross-platform, red teaming Command and Control (C2) framework. Its unique microservices architecture allows for high customization, making it a powerful tool for penetration testers and security researchers.

#### **Prerequisites**
1.  **A Ubuntu Server (Recommended):** 20.04 or 22.04 LTS. A fresh VM with **at least 2 vCPUs, 4 GB RAM, and 20 GB disk** is ideal.
2.  **Install Docker & Docker-Compose:** Mythic is deployed using Docker containers.

 

### **Step 1: Install Docker and Docker-Compose**

First, update your system and install the necessary Docker packages.

```bash
# Update the package list
sudo apt update && apt-get upgrade

# Update again and install Docker
sudo apt update
sudo apt install -y docker-compose

# Add your user to the docker group to run commands without sudo
sudo usermod -aG docker $USER# You will need to log out and back in for this to take effect.

# Verify installations
docker --version
docker compose version
```

 

### **Step 2: Install Mythic**

With Docker ready, you can now install Mythic.

1.  **Clone the Mythic Repository:**
    ```bash
    git clone https://github.com/its-a-feature/Mythic.git
    cd Mythic
    ```

2.  **Run the Mythic Installer:**
    This script will build the core Mythic containers.
    ```bash
    sudo ./install_docker_ubuntu.sh
    sudo make
    ```
    *   Follow the prompts. You can typically choose the default options.
    *   This process will take several minutes as it downloads and builds Docker images.

 

#### **Step 3: Start the Mythic Server**

After installation, use the Mythic CLI to start all the Docker containers.

```bash
# From the /Mythic directory, start the Mythic server in the background
./mythic-cli start
```
*   The `mythic-cli` is a helpful script for managing the Mythic environment.
*   This command will take a moment to start all the microservices.

 

#### **Step 4: Access the Web Interface**

Once the containers are running, you can access the Mythic control panel.

1.  Open a web browser on your computer.
2.  Navigate to:
    ```
    https://<YOUR_SERVER_IP>:7443
    ```
    *   Replace `<YOUR_SERVER_IP>` with the actual IP address of your Ubuntu server (e.g., `192.168.1.150`, `172.16.193.154`).
    *   **Use `https://`** and the port **`:7443`**.

3.  **You will see a security warning** because Mythic uses a self-signed SSL certificate. This is expected for a lab setup.
    *   In Chrome, click **"Advanced"** and then **"Proceed to..."**.
    *   In Firefox, click **"Accept the Risk and Continue"**.

4.  **Log in to the dashboard:**
    *   **Username:** `mythic_admin`
    *   **Password:** The password is in the file the `.env`
	*	Search for `~/Mythic# cat .env | grep MYTHIC_ADMIN_PASSWORD`

 

### **Essential Management Commands**

Keep these commands handy in your Mythic directory (`/Mythic`):

| Command | Description |
| :--- | :--- |
| `./mythic-cli start` | Start all Mythic containers |
| `./mythic-cli stop` | Stop all Mythic containers |
| `./mythic-cli status` | Check the status of all containers |
| `./mythic-cli logs <name>` | View logs for a specific container (e.g., `mythic_server`, `apollo`) |
| `./mythic-cli install github <url>` | Install a payload/agent from a GitHub URL |
| `./mythic-cli --help` | Show all available commands |

Your Mythic C2 server is now operational. Remember, this setup is for **educational and authorized testing purposes only** within a controlled lab environment.
