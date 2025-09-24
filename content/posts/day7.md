+++ 
draft = false
date = 2025-09-10T18:10:26+01:00
title = "Elastic Agent and Fleet Server Setup Tutorial, Day 7"
description = "SOC Analyst Challenge, Day 7 "
slug = "SOC Analyst Challenge, Day 7 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["cybersecurity"]
series = []
+++


## Day 7: Elastic Agent and Fleet Server Setup Tutorial
Ressources : [MyDFIR Channel](https://www.youtube.com/@MyDFIR/videos)
### **Architecture Overview**
*   **ELK Server (IP: 172.16.193.153):** Runs Elasticsearch and Kibana. Fleet management is done here.
*   **Fleet Server Host (IP: 172.16.193.157):** A dedicated server (Linux) that runs the Fleet Server component.
*   **Windows Server 2022 Host (IP: 172.16.193.155):** The server we want to monitor, which will run the Elastic Agent.

---

## **Part 1: Prerequisites - The ELK Server**
Ensure your ELK server (`172.16.193.153`) is fully operational and accessible.
1.  Elasticsearch is running and healthy.
2.  Kibana is running on port `5601` and you can log in.
3.  The servers that will host the Fleet Server and Elastic Agent can reach the ELK server on port `9200` (Elasticsearch) and `5601` (Kibana).

---

## **Part 2: Set Up the Fleet Server**

### **1. Access Fleet in Kibana**
1.  Open your browser and go to: `http://172.16.193.153:5601`.
2.  Navigate to **Fleet**:
    *   Click the main menu (☰) → **Management** → **Fleet**.

### **2. Add a Fleet Server**
The Fleet Server *is* an Elastic Agent, so it needs a policy to run. We create this policy first.

1.  In Fleet, go to the **Add Fleet Server** tab.
4.  **Configure the Fleet Server host:**
    *   This is the most important step. Give it a name , In the next field, you **must** specify the address that **other agents will use to connect to this Fleet Server**.
    *   If the server's IP is `172.16.193.157`, enter: `https://172.16.193.157:8220`
    *   *Remember this IP/hostname; you will need it for all agents.*
5.  **Select an Operating System:** Select **Linux** (assuming your Fleet Server host is Linux).
6.  **Copy the Install Command:** The command will look like the one below. **Copy it.**

```bash
sudo curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.13.0-linux-x86_64.tar.gz
sudo tar xzvf elastic-agent-8.13.0-linux-x86_64.tar.gz
cd elastic-agent-8.13.0-linux-x86_64
sudo ./elastic-agent install --url=https://172.16.193.153:8220 --fleet-server-es=https://172.16.193.153:9200 --fleet-server-service-token=AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL3Rva2VuLTE3MTU5Nzc4MTk1MTC --fleet-server-policy=fleet-server-policy --fleet-server-es-insecure --fleet-server-insecure-http
```

### **3. Run the Command on the Fleet Server Host**
1.  SSH into your  Linux fleet server (`172.16.193.157`).
2.  **Paste and run the entire command** you copied from Kibana.
3.  The script will download, install, and enroll the Elastic Agent, which will then start the Fleet Server service.

### **4. Verify in Kibana**
1.  Go back to the **Fleet** → **Agents** tab in Kibana.
2.  Within a minute, you should see a new agent enrolled. Its **Status** should be **Healthy**.
3.  Check the **Host** column – it should show the hostname of your Fleet Server .

### **Important:**
1.  In Kibana Fleet, go to the **Settings** tab.
2.  Go to  **Fleet server hosts** then actions and modify the port from 443 to 8220.
3.	If something went wrong verify the firewall:
    *   **Fleet Server Host (`172.16.193.157`):** Must allow **INBOUND** on port `8220` from all agents (and Kibana).
    *   **ELK Server (`172.16.193.153`):** Must allow **INBOUND** on port `9200` from the Fleet Server and agents.
---

## **Part 4: Add the Windows Server 2022 Agent**

Now we connect the Windows server to our new infrastructure.

### **1. Get the Installation Command for Windows**
1.  In Kibana Fleet, go to the **Agents** tab.
2.  Click **Add agent**, select **Enroll in fleet**
3.  **Select the Policy:** Choose the default **Elastic Cloud agent policy** or create a new one (e.g., `windows-servers`).
4.  **Select an Operating System:** Select **Windows**.
6.  **Copy the Install Command:** The command will be for PowerShell. **Copy it.**

```powershell
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.13.0-windows-x86_64.zip
Expand-Archive .\elastic-agent-8.13.0-windows-x86_64.zip
cd elastic-agent-8.13.0-windows-x86_64
.\elastic-agent.exe install --url=https://172.16.193.154:8220 --enrollment-token=SFMyNTY...
```

### **Important:** 
1. Verify that the Command  has port 8220 on it **--url=https://172.16.193.154:8220**.
2. Add --insecure at the end of the command. (because the command will use self-signed certificates, test purposes).


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
    *   **Fleet Server Host (`172.16.193.157`):** Must allow **INBOUND** on port `8220` from all agents (and Kibana).
    *   **ELK Server (`172.16.193.153`):** Must allow **INBOUND** on port `9200` from the Fleet Server and agents.
    *   **Agents:** Must be able to reach the Fleet Server on `8220` and Elasticsearch on `9200`.
*   **Fleet Server Host Setting:** The `Fleet Server host` value you set when adding an agent **must be the address that the agent uses to reach the Fleet Server**, which may be different from what Kibana uses.
*   **Logs:** If an agent isn't healthy, check its logs:
    *   **Linux:** `/opt/Elastic/Agent/data/elastic-agent.log`
    *   **Windows:** `C:\Program Files\Elastic\Agent\data\elastic-agent.log`



