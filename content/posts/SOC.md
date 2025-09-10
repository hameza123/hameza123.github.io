+++ 
draft = false
date = 2025-08-29T10:56:37+01:00
title = "30-Day MyDFIR SOC Analyst Challenge"
description = "This challenge is designed to help aspiring SOC Analysts like you gain hands-on practical experience in just 30 days. If you're looking to build your confidence and skills in cybersecurity, you're in the right place."
slug = ""
authors = []
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["cybersecurity"]
series = []
+++

# 30-Day MyDFIR SOC Analyst Challenge

## Objectives:

- how to draw a logical diagram
- how to setup ad configure ELK
- how to attack, detect and ivestigate
- how to create alerts ad dashboards
- how to setup and investigate ticketing system

## Day 1: Create diagrams, logical setup that will serve as the foundation for this 30-Day challenge

<img src="/images/diagram.png" style="display: block; margin: auto; max-width: 70%;" />

---

## Day 2: ELK Stack Introduction

First of all the 'E' stands for [Elastic Search](https://www.elastic.co/elasticsearch). Elasticsearch is an open source, distributed search and analytics engine built for speed, scale, and AI applications. As a retrieval platform, it stores structured, unstructured, and vector data in real time — delivering fast hybrid and vector search, powering observability and security analytics, and enabling AI-driven applications with high performance, accuracy, and relevance.

Then the 'L' stands for [Logstash](https://www.elastic.co/logstash). Logstash dynamically ingests, transforms, and ships your data regardless of format or complexity. Derive structure from unstructured data with grok, decipher geo coordinates from IP addresses, anonymize or exclude sensitive fields, and ease overall processing, using Beats or Elastic Agents.
 
Finally, the 'K' is for [Kibana](https://www.elastic.co/kibana). Run data analytics at speed and scale for observability, security, and search with Kibana. Powerful analysis on any data from any source, from threat intelligence to search analytics, logs to application monitoring, and much more.

Why using ELK stack:

- Centralized logging  
- Flexibility (constomized ingestion)
- Visualization
- Scalability
- Ecosystem (many integrations and community)
- Siems

---

## Day 3: ELK Stack Istallation

### Create private network

1. Open VMware Fusion Preferences

	- Launch VMware Fusion.
	- From the menu bar, navigate to Window > Virtual Machine Library.
	- With the library open, go to the VMware Fusion menu in the menu bar and click Preferences....

2. Access the Network Settings

	In the Preferences window, click on the Network icon (it resembles a globe with a network cable).

3. Unlock the Settings for Changes

	- Click the lock icon in the bottom-left corner of the window.
	- Enter your Mac's administrator password when prompted to allow changes.

4. Create a New Custom Network

	- Click the + (plus) button located below the list of existing networks.
	- A new network entry (e.g., vmnet3) will appear in the list. Select it to configure its properties.

5. Configure the "Private Network"

 	With the new vmnet selected, configure the settings on the right-hand side:

	- Name: Change the default name to Private Network.
	- Subnet IP: You can keep the automatically generated address (e.g., 192.168.xxx.0) or define your own (e.g., 10.0.10.0).
	- Subnet Mask: The default 255.255.255.0 is recommended for a standard Class C network.
	- Ensure the checkbox for "Provide addresses on this network via DHCP" is selected. This is crucial for automatically assigning IP addresses to VMs.

6. Apply the New Configuration

	Click the Apply button to save and activate your new "Private Network".

### Create ELK server

1. Download Ubuntu Server ISO

	- Open a web browser and go to the [Ubuntu Server download page](https://ubuntu.com/download/server).
	- Download the latest LTS version (e.g., 64-bit PC (AMD64) server install image).

2. Open VMware Fusion and Start Creation

	- Launch VMware Fusion.
	- From the menu bar, click File > New.
	- Alternatively, in the Virtual Machine Library, click the + button and choose New.

3. Choose Installation Method

	- Select Create a custom virtual machine and click Continue.

4. Choose Operating System

	- Select Linux as the guest operating system.
	- From the Version dropdown, choose Ubuntu 64-bit.
	- Click Continue.

5. Select Firmware Type

	- Choose UEFI (recommended) or BIOS (legacy) and click Continue.

6. Choose Virtual Disk Type

	- Select SCSI (recommended) and click Continue.

7. Create a New Virtual Disk

	- Ensure Create a new virtual disk is selected.
	- Set the maximum disk size (e.g., 20 GB).
	- Choose Split into multiple files (recommended for portability).
	- Click Continue.

8. Customize Settings Before Installation

	Before clicking Finish, ensure the checkbox for Power on this virtual machine after creation is unchecked.
	Click Customize Settings.

9. Configure Virtual Machine Hardware

 	A new settings window will open. Configure the following:

	9.1. General

		- Name: Ubuntu Server (or your preferred name).

	9.2. Processors & Memory

		- Processors: Assign at least 2 CPU cores.
		- Memory: Assign at least 2048 MB (2 GB) of RAM.

	9.3. Hard Disk (SCSI)

		- You can adjust the disk size here if needed.

	9.4. CD/DVD (IDE)

		- Change the setting from Auto detect to Choose a disk or image....
		- Navigate to and select the downloaded Ubuntu Server ISO file.
		- Ensure Connect CD/DVD Drive is checked.

	9.5. Network Adapter

	For a network connection, ensure the adapter is connected to:

		- Bridged Network (to be like a separate device on your network), or
		- Share with my Mac (NAT) for internet access.

10. Save and Start the Virtual Machine

	- Close the settings window to save all configuration.
	- In the Virtual Machine Library, select your new Ubuntu Server VM.
	- Click the Play button to power it on.

11. Install Ubuntu Server

	The VM will boot from the ISO and start the Ubuntu Server installer.
	Follow the on-screen instructions to complete the installation:

	- Choose your language.
	- Configure keyboard layout.
	- Set up network connections (if needed).
	- Configure storage (use the entire virtual disk).
	- Set up your user profile and server name.
	- Select which software to install (e.g., SSH server).
	- When the installation is complete, the installer will prompt you to reboot.

12. Final Reboot and Login

 	The VM will reboot. Eject the installation media:

	- Go to the VMware Fusion menu bar: Virtual Machine > CD/DVD > Disconnect ISO image.
	- The VM will now boot from its new virtual hard disk.
	- Log in with the username and password you created during installation.

### Installing Elastic Search

1. Download the Package

	- Go to the official [Elasticsearch](https://www.elastic.co/downloads/elasticsearch) Download page.
	- Under "Linux x86_64", right-click on the DEB package link and select Copy Link Address.

	The version number in the link might be different (e.g., 9.1.3).

2. Download on Your Ubuntu Server

	Open a terminal on your Ubuntu server and use wget with the copied link to download the package.

	```bash
	sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.3-amd64.deb
	```

3. Install the Downloaded Package

	Use the dpkg command to install the package. This will set up Elasticsearch and its required directories.

	```bash
	sudo dpkg -i elasticsearch-9.1.3-amd64.deb
	```

4. Modify the file elasticsearch.yml 

	Go to /etc/elasticsearch, then elasticsearch.yml and modify this two lines on the network section: 
	```bash
	---------------------------------- Network -----------------------------------
	#
	# By default Elasticsearch is only accessible on localhost. Set a different
	# address here to expose this node on the network:
	#
	network.host: your ip address
	#
	# By default Elasticsearch listens for HTTP traffic on the first free port it
	# finds starting at 9200. Set a specific HTTP port here:
	#
	http.port: 9200

	```


5. Reload systemd and Start Elasticsearch

	After installation, you must reload the systemd manager configuration to recognize the new service, then start it.

	```bash
	sudo systemctl daemon-reload
	sudo systemctl enable elasticsearch.service
	sudo systemctl start elasticsearch.service
	```


6. Verify the Installation

	Check if the Elasticsearch service is running correctly.

	```bash
	sudo systemctl status elasticsearch.service
	```

---

## Day 4: Installing Kibana

1. Download the Package

	- Go to the official [Kibana](https://www.elastic.co/downloads/kibana) Download page.
	- Under "Linux x86_64", right-click on the DEB package link and select Copy Link Address.

	The version number in the link might be different (e.g., 9.1.3).

2. Download on Your Ubuntu Server

	Open a terminal on your Ubuntu server and use wget with the copied link to download the package.

	```bash
	sudo wget https://artifacts.elastic.co/downloads/kibana/kibana-9.1.3-amd64.deb
	```

3. Install the Downloaded Package

	Use the dpkg command to install the package. This will set up Elasticsearch and its required directories.

	```bash
	sudo dpkg -i kibana-9.1.3-amd64.deb 
	```

4. Configure Kibana

	Edit the Kibana configuration file to allow external connections and point to your Elasticsearch instance.

	```bash
	sudo nano /etc/kibana/kibana.yml
	```
	```bash
	# =================== System: Kibana Server ===================
	# Kibana is served by a back end server. This setting specifies the port to use.
	server.port: 5601

	# Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
	# The default is 'localhost', which usually means remote machines will not be able to connect.
	# To allow connections from remote users, set this parameter to a non-loopback address.
	server.host: your ip address
	```
	Save the file and exit (Ctrl+X, then Y, then Enter).


5. Enable and Start the Kibana Service

	After installation, you must reload the systemd manager configuration to recognize the new service, then start it.

	```bash
	sudo systemctl daemon-reload
	sudo systemctl enable kibana.service
	sudo systemctl start kibana.service
	```

6. Verify the Installation

	Check if the Elasticsearch service is running correctly.

	```bash
	sudo systemctl status elasticsearch.service
	```

7. Generate the Kibana Enrollment Token

	Kibana needs a token to enroll itself with Elasticsearch's security features. Generate this token on the Elasticsearch server.

	```bash
	# Navigate to the Elasticsearch bin directory and generate the token
	cd /usr/share/elasticsearch/bin
	sudo ./elasticsearch-create-enrollment-token --scope kibana
	```

	Copy the long token string that is output. You will need it for the next step.

8. Complete Kibana Setup in the Web Browser

		- Open a web browser on your host machine (e.g., your Mac).
		- Navigate to http://<your-ubuntu-server-ip>:5601 (e.g., http://192.168.1.100:5601).
		- You will be prompted for the Enrollment Token. Paste the token you copied in the previous step and click Enroll.

	The setup process will now ask for a verification code. This code is generated on the Kibana server.

	```bash
	# Navigate to the Kibana bin directory and generate the code
	cd /usr/share/kibana/bin
	sudo ./kibana-verification-code
	```

	Copy the 6-digit code that is displayed. Enter it in the browser window.

9. Log in to Kibana

	 You will now be prompted to log in.

		- Username: elastic
		- Password: Use the password you saved during the Elasticsearch installation. If you did not save it, you can reset it using the elasticsearch-reset-password tool.

10. Generate Encryption Keys

	For production-like environments, you should generate encryption keys for Kibana's saved objects and store them in its keystore.


	```bash
	# Navigate to the Kibana bin directory
	cd /usr/share/kibana/bin

	# Generate the encryption keys. Copy the output.
	sudo ./kibana-encryption-keys generate

	# Add each generated key to the Kibana keystore
	sudo ./kibana-keystore add xpack.encryptedSavedObjects.encryptionKey
	# Enter the value for xpack.encryptedSavedObjects.encryptionKey when prompted.

	sudo ./kibana-keystore add xpack.reporting.encryptionKey
	# Enter the value for xpack.reporting.encryptionKey when prompted.

	sudo ./kibana-keystore add xpack.security.encryptionKey
	# Enter the value for xpack.security.encryptionKey when prompted.
	```

	After all configuration, restart both services to ensure all changes take effect.

	```bash
	sudo systemctl restart kibana.service
	sudo systemctl restart elasticsearch.service
	```

	You have now successfully installed Kibana and connected it to your Elasticsearch node. You can access the Kibana dashboard at http://<your-ubuntu-server-ip>:5601 to begin visualizing your data.



 
---  

## Day 5: Windows Server 2022 Installation

## Part 1: The Installation

Go to [Windows Server 2022 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022) and select the **ISO** option for your desired edition (e.g., **Standard**), 

### 1. Open VMware Fusion and Start Creation
- Launch **VMware Fusion**.
- From the menu bar, click **File** > **New**.
- Click **Create a custom virtual machine**. This gives you full control over the configuration. Click **Continue**.

### 2. Choose Operating System
- Select **Windows** as the guest operating system.
- From the Version dropdown, scroll down and select **Windows Server 2022** or **Windows Server 2022 (64-bit)**.
- Click **Continue**.

### 3. Choose Firmware Type
- Select **UEFI** (Highly recommended for modern Windows systems). Click **Continue**.

### 4. Choose Virtual Disk Type
- Select **NVMe** (recommended for performance) or **SCSI**. Click **Continue**.

### 5. Create a New Virtual Disk
- Ensure **Create a new virtual disk** is selected.
- Set the maximum disk size. **80 GB** is a good starting point for a server with a GUI.
- Choose **Split into multiple files** (recommended for portability).
- Click **Continue**.

### 6. Finalize Creation & Configure Settings
- **Before clicking Finish:** Uncheck the box for **Power on this virtual machine after creation**.
- Click **Customize Settings**.
- A save dialog will appear. Name your VM (e.g., `Win-Server-2022`) and choose a location. Click **Save**.

### 7. Configure Virtual Machine Hardware
A settings window will open. Configure the following key components:

#### **7.1. Processors & Memory**
- **Processors:** Assign at least **2** CPU cores. 4 or more is better for performance.
- **Memory:** Assign at least **4096 MB** (4 GB) of RAM. **8 GB (8192 MB) is strongly recommended** for a smooth experience, especially with the Desktop Experience GUI.

#### **7.2. Hard Disk (NVMe or SCSI)**
- You can adjust the size here if needed.

#### **7.3. CD/DVD (IDE) - MOST IMPORTANT STEP**
- Change the setting from **Auto detect** to **Choose a disk or image...**.
- Navigate to and select the downloaded **Windows Server 2022 ISO** file.
- Ensure **Connect CD/DVD Drive** is checked.

#### **7.4. Network Adapter**
- Ensure the adapter is connected to:
    - **Bridged Networking** (Recommended): Gives the VM its own IP on your network, like a physical machine.
    - **Share with my Mac** (NAT): Hides the VM behind your Mac's IP. Good if you don't need direct network access.

#### **7.5. (Optional) Add a Serial Port for Console Access**
- Click the **+** button at the bottom.
- Select **Serial Port**.
- Choose **Output to named pipe** and use the default path (e.g., `/tmp/win-server-serial`). Useful for headless management.

### 8. Save and Start the Installation
- Close the settings window to save the configuration.
- In the Virtual Machine Library, select your new **Windows Server 2022** VM.
- Click the **Play** button to power it on.

### 9. Install Windows Server 2022
The VM will boot from the ISO and start the Windows Server installer.

1.  **Language & Preferences:** Choose your language, time, and keyboard input. Click **Next**.
2.  **Install Now:** Click the **Install Now** button.
3.  **Select OS Edition:** Choose your edition (e.g., **Windows Server 2022 Standard Evaluation (Desktop Experience)** for a full GUI) and click **Next**.
4.  **License Terms:** Accept the license terms and click **Next**.
5.  **Installation Type:** Select **Custom: Install Windows only (advanced)**.
6.  **Disk Partitioning:** You will see your virtual disk. Select it and click **Next**. The installer will automatically create the necessary partitions.
7.  **Wait for Installation:** The OS files will copy and install. The VM will reboot several times automatically.

### 10. Post-Installation Setup
After the install, the VM will boot into the Out-of-Box Experience (OOBE).

1.  **Administrator Password:** You will be prompted to set a password for the built-in **Administrator** account. **Create a strong password and store it securely.**
2.  **Ctrl+Alt+Delete:** Press **Ctrl+Alt+Delete** to log in. In VMware Fusion, you can do this from the menu: **Virtual Machine** > **Send Ctrl-Alt-Del**.
3.  **Login:** Enter the password for the Administrator account.

### 11. Install VMware Tools (Essential)
VMware Tools provides crucial drivers for better graphics, mouse performance, network, and shared folders.

- After logging in, a VMware Tools installation dialog might appear automatically. If it does, follow the prompts.
- If it doesn't, go to the VMware Fusion menu bar: **Virtual Machine** > **Install VMware Tools**.
- Follow the on-screen instructions in the Windows VM to complete the installation. **Reboot when prompted.**

### 12. Configure Network (If Necessary)
- Open **Server Manager** > **Local Server**.
- Click on the Ethernet link (e.g., "Ethernet0") to configure network settings (static IP, DNS, etc.) if you are not using DHCP.

### 13. (Optional) Activate Windows
- If you have a product key, you can activate it via **Settings** > **Activation**.
- If using the evaluation, you have a 180-day trial period.

## Initial Configuration Checklist
Once logged in, perform these initial tasks:
-   **Windows Update:** Install all available updates.
-   **Computer Name:** Change the server's name in **System Properties**.
-   **Join Domain (Optional):** If you have an Active Directory domain, join the server to it.
-   **Add Roles & Features:** Use Server Manager to install roles like AD DS, DNS, DHCP, IIS, etc.

Your Windows Server 2022 virtual machine is now ready for use.	    

## Part 2: Activate RDP on Windows Server 2022

### 1. Enable Remote Desktop
1.  Log into your Windows Server 2022 VM.
2.  Open **Server Manager** (it usually opens automatically).
3.  In the left pane, click on **Local Server**.
4.  In the main properties pane, find the **Remote Desktop** property. It will likely say "Disabled".
5.  Click on the word **"Disabled"**.

### 2. Configure Remote Desktop Settings
A **System Properties** window will open to the "Remote" tab.

1.  Select the option: **Allow remote connections to this computer**.
2.  **Uncheck** the box that says **Allow connections only from computers running Remote Desktop with Network Level Authentication (recommended)**.
    *   *We uncheck this initially for simpler troubleshooting. You can re-enable it later for better security once you confirm it works.*
3.  A warning pop-up will appear. Click **OK**.
4.  Click **Apply** and then **OK** to close the window.

### 3. Configure the Windows Firewall (Crucial Step)
The Windows Firewall is on by default and will block RDP connections if not configured.

1.  Press the `Win` key, type **Windows Defender Firewall**, and open it.
2.  In the left pane, click **Advanced settings**.
3.  In the **Windows Defender Firewall with Advanced Security** window, click on **Inbound Rules** in the left pane.
4.  In the list of rules, find the rules named **Remote Desktop - User Mode (TCP-In)** and **Remote Desktop - User Mode (UDP-In)**.
5.  For both of these rules, right-click each one and select **Enable Rule**. Ensure they are enabled for the "Domain" and "Private" profiles (you can also enable "Public" if needed, though it's less secure).

### 4. Find Your Server's IP Address
You will need this IP address to connect from your Mac.

1.  Press `Win + R`, type `cmd`, and press Enter to open a Command Prompt.
2.  In the command prompt, type `ipconfig` and press Enter.
3.  Look for the **IPv4 Address** under your active network adapter (e.g., `Ethernet0`). It will look something like `192.168.1.150`.
4.  **Write this IP address down.**

## Part 3: Test RDP from Your Mac using Microsoft Remote Desktop

### 1. Install Microsoft Remote Desktop
The official Microsoft client is free on the Mac App Store.

1.  Open the **App Store** on your Mac.
2.  Search for **"Microsoft Remote Desktop"**.
3.  Click **Get** and then **Install**. 

### 2. Add a PC Connection
1.  Open the **Microsoft Remote Desktop** app.
2.  Click the **Add PC** button.

    

3.  In the window that appears:
    *   **PC name:** Enter the IP address of your Windows Server (e.g., `192.168.1.150`).
    *   **User account:** Click the dropdown and select **Add User Account...**.
        *   Enter the username: `Administrator`
        *   Enter the password you set for the Administrator account during Windows Server setup.
        *   Click **Add**.
4.  Leave other settings as default for now. Click **Add**.

### 3. Connect to Your Server
1.  Back in the main window of the Remote Desktop app, you will see an icon for your server under "My Desktops".
2.  Double-click on the icon to initiate the connection.
3.  You may see a certificate warning. Check the box **"Don't ask me again for connections to this computer"** and click **Continue**.

### 4. Troubleshooting Connection Issues
If you cannot connect, check the following on the Windows Server VM:

*   **Firewall:** Double-check that the **Remote Desktop - User Mode (TCP-In)** rule is enabled in the Windows Firewall's Inbound Rules.
*   **IP Address:** Ensure you are using the correct IPv4 address from `ipconfig`.
*   **Network Type:** Ensure your VM's network adapter in VMware Fusion is set to **Bridged** (not "NAT") for easiest connectivity. If using NAT, you may need to set up port forwarding.
*   **RDP Setting:** Go back to the System Properties > Remote tab and ensure **"Allow remote connections..."** is selected.

Your connection from the Mac should still work, as the Microsoft Remote Desktop client supports NLA.

---

## Day 6: Elastic Agent and Fleet Server Introduction


### **Elastic Agent and Fleet Server: A Centralized Management Revolution**

#### **1. The Problem They Solve**

Traditionally, collecting logs, metrics, and security data from servers involved installing multiple lightweight data shippers (like Beats: Filebeat, Metricbeat, Winlogbeat, etc.) and managing each one individually. This approach became complex and hard to maintain at scale:
*   **Management Overhead:** Updating configuration files across thousands of servers.
*   **Resource Usage:** Running multiple independent processes.
*   **Orchestration Complexity:** Coordinating many different agents.

#### **2. The Solution: A Single, Unified Agent**

**Elastic Agent** is a single, unified agent that you can deploy to your hosts (servers, containers, laptops) to collect data and send it to the Elastic Stack.

*   **All-in-One:** It consolidates the functionality of all the individual Beats and more into one agent.
*   **Centralized Management:** Its configuration is not managed on the host itself but is **centrally controlled from Kibana** via a feature called **Fleet**.
*   **Policy-Driven:** You define a **policy** (what data to collect, what to do with it) in Kibana, and Fleet pushes that policy to all your Elastic Agents automatically.

#### **3. The Brain: Fleet in Kibana**

**Fleet** is the web-based UI inside Kibana where you manage your entire fleet of Elastic Agents.
*   You add **integrations** (pre-built packages for collecting data from sources like Nginx, AWS, MySQL, Windows Event Logs, etc.).
*   You create **agent policies** that combine these integrations and define settings like the output (where to send data).
*   You get a full overview of all your agents: their status, version, and last check-in time.

#### **4. The Coordinator: Fleet Server**

This is the critical link between Fleet (in Kibana) and the Elastic Agents running on your hosts.

*   **Purpose:** The Fleet Server acts as a **secure communication gateway**.
*   **How it Works:**
    1.  You install a Fleet Server (which is itself a specialized Elastic Agent) in your environment.
    2.  All your other Elastic Agents do **not** talk directly to Kibana or Elasticsearch. Instead, they **check in with the Fleet Server**.
    3.  The Fleet Server relays information *from* the agents *to* Kibana (e.g., agent status) and, most importantly, it delivers new policies *from* Kibana *to* the agents.

*   **Deployment:** The Fleet Server **must** be installed on a host that is highly available and has a low-latency connection to both your other agents and your Elasticsearch cluster. It's often installed on its own dedicated VM or a Kubernetes pod.


### **Why This Architecture is Powerful**

1.  **Scalability:** The Fleet Server handles the communication load, preventing your Elasticsearch cluster from being overwhelmed by thousands of agents checking in directly.
2.  **Security:** Agents only need network access to the Fleet Server and Elasticsearch, not to Kibana. This reduces the attack surface.
3.  **Efficiency:** Centralized management means you can change what data is collected across your entire infrastructure with just a few clicks in a web UI, without ever logging into a server.
4.  **Reliability:** The agent is self-healing. If it gets into a bad state, it will automatically restart itself and reconnect to the Fleet Server.


### **Visualizing the Data Flow**

The entire architecture can be summarized in this data flow:

<img src="/images/fleet.png" style="display: block; margin: auto; max-width: 80%;" />


**In summary:**
*   **Elastic Agent** is the worker that collects data on your hosts.
*   **Fleet** is the mission control center in Kibana.
*   **Fleet Server** is the dispatcher that ensures instructions from mission control reach the workers and that status updates from the workers get back.

This modernized approach is the foundation for the Elastic Stack's capabilities in observability and security, enabling you to manage vast, distributed infrastructures with ease.


---

## Day 7: Elastic Agent and Fleet Server Setup Tutorial

### **Architecture Overview**
*   **ELK Server (IP: 172.16.193.153):** Runs Elasticsearch and Kibana. Fleet management is done here.
*   **Fleet Server Host (New VM):** A dedicated server (Linux) that runs the Fleet Server component.
*   **Windows Server 2022 Host (New VM):** The server we want to monitor, which will run the Elastic Agent.

---

## **Part 1: Prerequisites - The ELK Server**
Ensure your ELK server (`172.16.193.153`) is fully operational and accessible.
1.  Elasticsearch is running and healthy.
2.  Kibana is running on port `5601` and you can log in.
3.  The servers that will host the Fleet Server and Elastic Agent can reach the ELK server on port `9200` (Elasticsearch) and `5601` (Kibana).

---

## **Part 2: Configure Kibana for Fleet & Generate Fleet Server Policy**

### **1. Access Fleet in Kibana**
1.  Open your browser and go to: `http://172.16.193.153:5601`.
2.  Navigate to **Fleet**:
    *   Click the main menu (☰) → **Management** → **Fleet**.

### **2. Quick Initial Setup (If prompted)**
If it's your first time in Fleet, you might be guided through a quick setup. The most critical step is to **add an Elasticsearch output**. It should already be configured to use your local Elasticsearch (`http://localhost:9200`). Verify this.

### **3. Generate a Fleet Server Policy**
The Fleet Server *is* an Elastic Agent, so it needs a policy to run. We create this policy first.

1.  In Fleet, go to the **Agent Policies** tab.
2.  Click **Create agent policy**.
3.  Give it a descriptive name: `Fleet Server Policy`.
4.  **Toggle on** **"Agent monitoring"** and **"Fleet Server"**.
    *   Enabling "Fleet Server" is the key. This tells the agent to run the special Fleet Server service.
5.  **Uncheck** **"Collect system logs and metrics"**. We want this agent to be dedicated to running the Fleet Server, not collecting host data.
6.  Click **Create policy**.

---

## **Part 3: Set Up the Fleet Server Host**

You need a new server (Linux is recommended) dedicated to running the Fleet Server. It must have network connectivity to *both* the ELK server *and* the agents it will manage.

### **1. Get the Installation Command for Fleet Server**
1.  In Kibana Fleet, go to the **Agents** tab.
2.  Click **Add agent**.
3.  **Select the Policy:** Choose the `Fleet Server Policy` you just created from the dropdown.
4.  **Configure the Fleet Server host:**
    *   This is the most important step. In the `Fleet Server host` input box, you **must** specify the address that **other agents will use to connect to this Fleet Server**.
    *   If the server's IP is `172.16.193.154`, enter: `https://172.16.193.154:8220`
    *   *Remember this IP/hostname; you will need it for all agents.*
5.  **Select an Operating System:** Select **Linux** (assuming your Fleet Server host is Linux).
6.  **Copy the Install Command:** The command will look like the one below. **Copy it.**

```bash
sudo curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.13.0-linux-x86_64.tar.gz
sudo tar xzvf elastic-agent-8.13.0-linux-x86_64.tar.gz
cd elastic-agent-8.13.0-linux-x86_64
sudo ./elastic-agent install --url=https://172.16.193.153:8220 --fleet-server-es=https://172.16.193.153:9200 --fleet-server-service-token=AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL3Rva2VuLTE3MTU5Nzc4MTk1MTC --fleet-server-policy=fleet-server-policy --fleet-server-es-insecure --fleet-server-insecure-http
```

### **2. Run the Command on the Fleet Server Host**
1.  SSH into your new Linux server (`172.16.193.154`).
2.  **Paste and run the entire command** you copied from Kibana.
3.  The script will download, install, and enroll the Elastic Agent, which will then start the Fleet Server service.

### **3. Verify in Kibana**
1.  Go back to the **Fleet** → **Agents** tab in Kibana.
2.  Within a minute, you should see a new agent enrolled. Its **Status** should be **Healthy**.
3.  Check the **Host** column – it should show the hostname of your Fleet Server host.

---

## **Part 4: Add the Windows Server 2022 Agent**

Now we connect the Windows server to our new infrastructure.

### **1. Get the Installation Command for Windows**
1.  In Kibana Fleet, go to the **Agents** tab.
2.  Click **Add agent**.
3.  **Select the Policy:** Choose the default **Elastic Cloud agent policy** or create a new one (e.g., `windows-servers`).
4.  **Configure the Fleet Server host:** This is critical. Enter the address of the Fleet Server you just set up: `https://172.16.193.154:8220`
5.  **Select an Operating System:** Select **Windows**.
6.  **Copy the Install Command:** The command will be for PowerShell. **Copy it.**

```powershell
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.13.0-windows-x86_64.zip
Expand-Archive .\elastic-agent-8.13.0-windows-x86_64.zip
cd elastic-agent-8.13.0-windows-x86_64
.\elastic-agent.exe install --url=https://172.16.193.154:8220 --enrollment-token=SFMyNTY...
```

### **2. Run the Command on Windows Server 2022**
1.  Log into your Windows Server 2022 VM.
2.  Open **PowerShell as an Administrator**.
3.  **Paste and run the entire command** you copied from Kibana.
4.  The installer will download and install the Elastic Agent as a Windows service. It will enroll with the Fleet Server and receive its policy.

### **3. Verify in Kibana and Check Data**
1.  Go back to the **Fleet** → **Agents** tab in Kibana.
2.  You should now see a second agent listed. Its **Status** should become **Healthy**.
3.  The **Host** column will show the hostname of your Windows server.
4.  To see data, go to **Analytics** → **Discover**. Select the `logs-*`, `metrics-*`, or `.ds-logs-system.syslog-*` data view to see logs and metrics from your Windows host.

---

## **Troubleshooting & Key Points**

*   **Firewalls are the #1 Enemy:** Ensure all necessary ports are open:
    *   **Fleet Server Host (`172.16.193.154`):** Must allow **INBOUND** on port `8220` from all agents (and Kibana).
    *   **ELK Server (`172.16.193.153`):** Must allow **INBOUND** on port `9200` from the Fleet Server and agents.
    *   **Agents:** Must be able to reach the Fleet Server on `8220` and Elasticsearch on `9200`.
*   **Fleet Server Host Setting:** The `Fleet Server host` value you set when adding an agent **must be the address that the agent uses to reach the Fleet Server**, which may be different from what Kibana uses.
*   **Logs:** If an agent isn't healthy, check its logs:
    *   **Linux:** `/opt/Elastic/Agent/data/elastic-agent.log`
    *   **Windows:** `C:\Program Files\Elastic\Agent\data\elastic-agent.log`












## Additional Resources:


[Cloud Native Trainer with Mike](https://www.youtube.com/watch?v=zrmeOu8DYyw) 

[Git crash course ]()

