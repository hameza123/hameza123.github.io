+++ 
draft = false
date = 2025-09-12T19:12:58+01:00
title = "Day 20: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 20 "
slug = "SOC Analyst Challenge, Day 20"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++


## Day 20: Mythic Server Setup Tutorial
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

Of course. Here is a comprehensive tutorial for setting up a Mythic C2 server on a Linux host.

---

### **Mythic C2 Server Setup Tutorial**

#### **What is Mythic?**
Mythic is a sophisticated, cross-platform, red teaming Command and Control (C2) framework. Its unique microservices architecture allows for high customization, making it a powerful tool for penetration testers and security researchers.

#### **Prerequisites**
1.  **A Ubuntu Server (Recommended):** 20.04 or 22.04 LTS. A fresh VM with **at least 2 vCPUs, 4 GB RAM, and 20 GB disk** is ideal.
2.  **Install Docker & Docker-Compose:** Mythic is deployed using Docker containers.

---

### **Step 1: Install Docker and Docker-Compose**

First, update your system and install the necessary Docker packages.

```bash
# Update the package list
sudo apt update

# Install prerequisites
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add the Docker repository
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update again and install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Add your user to the docker group to run commands without sudo
sudo usermod -aG docker $USER
# You will need to log out and back in for this to take effect.

# Install Docker Compose plugin
sudo apt install -y docker-compose-plugin

# Verify installations
docker --version
docker compose version
```

---

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
    ```
    *   Follow the prompts. You can typically choose the default options.
    *   This process will take several minutes as it downloads and builds Docker images.

---

### **Step 3: Start Mythic and Access the Web Interface**

1.  **Start the Mythic Server:**
    From within the `Mythic` directory, run:
    ```bash
    sudo docker compose up -d
    ```
    The `-d` flag runs the containers in the background.

2.  **Check Container Status:**
    Ensure all containers are running without errors.
    ```bash
    sudo docker compose logs --tail=50 mythic_server
    sudo docker compose ps
    ```

3.  **Access the Web UI:**
    *   Open a web browser on your local machine.
    *   Navigate to **`https://<your-server-ip>:7443`**
        *   Example: `https://192.168.1.150:7443`
    *   You will see a security warning because Mythic uses a self-signed certificate. This is normal for a lab. **Proceed anyway** (in Chrome, click "Advanced" -> "Proceed to...").

4.  **Log In:**
    *   **Default Username:** `mythic_admin`
    *   **Default Password:** The password was generated during installation. You can find it in the logs:
        ```bash
        sudo docker compose logs mythic_server | grep "Default password"
        ```
    *   **You will be forced to change the password on first login.**

---





Of course. You've provided the concise, correct commands. Let's break them down into a clear, step-by-step tutorial based on your instructions.

### **Mythic C2 Server Setup: Quick & Correct Method**

This tutorial assumes you are on a fresh Ubuntu 22.04/24.04 server.

---

#### **Step 1: Install Prerequisites (Git & Docker)**

Mythic requires Docker to be installed first.

```bash
# Update the system
sudo apt update && sudo apt upgrade -y

# Install Git and other helpers
sudo apt install -y git

# Install Docker using the official script (this is the recommended method)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add your user to the 'docker' group to run commands without sudo
sudo usermod -aG docker $USER
# You MUST log out and back in for this to take effect, or run the next commands with 'sudo'.
```

---

#### **Step 2: Clone and Install Mythic**

This downloads the Mythic code and runs its installation script.

```bash
# 1. Clone the Mythic repository from GitHub
git clone https://github.com/its-a-feature/Mythic.git

# 2. Change into the Mythic directory
cd Mythic

# 3. Run the Mythic installation script for Ubuntu
# This script installs dependencies and builds the core containers.
./install_docker_ubuntu.sh
```
*   During the script, you will be prompted to set a `MYTHIC_ADMIN_PASSWORD`. **Choose a strong password and remember it.** If you don't set one, a random password will be generated (check logs for it).
*   The script will also ask if you want to include example payloads. You can say `no` for a minimal install; you can add them later from the UI.

---

#### **Step 3: Start the Mythic Server**

After installation, use the Mythic CLI to start all the Docker containers.

```bash
# From the /Mythic directory, start the Mythic server in the background
./mythic-cli start
```
*   The `mythic-cli` is a helpful script for managing the Mythic environment.
*   This command will take a moment to start all the microservices.

---

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
    *   **Password:** The password you set during the `./install_docker_ubuntu.sh` script. If you didn't set one, find it in the logs with:
        ```bash
        ./mythic-cli logs mythic_server | grep "Default password"
        ```

You are now in the Mythic UI. The next steps are to install a Payload Type (like Apollo for Windows), create a listener, and generate a payload.

---

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





---

### **Step 4: Install a Payload Type (Agent)**

Mythic is modular. Out of the box, it doesn't come with any agents (called "Payload Types"). You must install them.

1.  **In the Mythic UI,** go to **Settings -> Payload Types**.
2.  You will see a list of available agents. Popular choices include:
    *   **Apollo:** A powerful, feature-rich agent written in .NET.
    *   **Athena:** A modern, cross-platform agent written in Rust.
    *   **Poseidon:** A Python-based agent.
3.  Click **Install** next to your desired agent(s). This will trigger Docker to build the new container for that agent. This will take a few minutes.

You can monitor the build process via the logs:
```bash
sudo docker compose logs -f --tail=20 <payload_type_name>  # e.g., apollo
```

---

### **Step 5: Create an Agent and Generate a Payload**

Now, generate an executable that you can deploy on a target machine.

1.  **Create a Payload:**
    *   In the Mythic UI, go to **Payloads -> Create Payload**.
    *   **Select the Payload Type** you installed (e.g., `apollo`).
    *   **Choose a C2 Profile:** For simplicity, start with `http` or `dynamic_http`.
    *   **Select the Operating System** for your target (e.g., Windows).
    *   Click **Create**. Mythic will generate the payload.

2.  **Download the Payload:**
    *   Once generated, you will be redirected to the payload page.
    *   Click the **Download** button to get the executable (e.g., a `.exe` for Windows).

---

### **Step 6: Listen for Connections**

Before executing the payload, you need to set up a listener for it to call back to.

1.  **Go to Listeners -> Create Listener**.
2.  **Select a C2 Profile:** Choose the same one you used for the payload (e.g., `http`).
3.  **Configure the Listener:**
    *   **Name:** Give it a descriptive name (e.g., `My-HTTP-Listener`).
    *   **Host:** This should be the **IP address of your Mythic server** that the agent can reach (e.g., `192.168.1.150`). **Do not use `localhost` or `127.0.0.1` here.**
4.  Click **Start Listener**.

---

### **Step 7: Test the Agent**

1.  **Transfer** the downloaded payload to your target test machine (e.g., a Windows VM).
2.  **Execute the payload** on the target machine.
3.  **Check for Callback:**
    *   In the Mythic UI, go to **Operators -> Active Connections**.
    *   You should see a new agent check-in appear in the table. You can now interact with it.

    

### **Basic Mythic Commands**

To stop, start, or update Mythic, use these commands from the `Mythic` directory:

*   **Stop Mythic:** `sudo docker compose down`
*   **Start Mythic:** `sudo docker compose up -d`
*   **View Logs:** `sudo docker compose logs -f --tail=50 <container_name>`
*   **Update Mythic:** `git pull` followed by `sudo ./install_docker_ubuntu.sh` and `sudo docker compose up -d`

**Important Security Note:** This setup is for a **lab environment only**. Exposing Mythic to the internet without proper hardening and obfuscation (e.g., using domain fronting, custom C2 profiles) will lead to quick detection.