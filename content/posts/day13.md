+++ 
draft = false
date = 2025-09-11T09:35:17+01:00
title = "Day 13: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 13 "
slug = "SOC Analyst Challenge, Day 13 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge","Elastic Agent"]
categories = ["SOC","Elastic Agent"]
series = []
+++


## Day 13: How To Install Elastic Agent on Ubuntu
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

 

### **Prerequisites**
1.  **An Ubuntu Server** (22.04 LTS or 24.04 LTS) with internet access.
2.  **A Running Elastic Stack:** Kibana and Elasticsearch must be running and accessible.
3.  **A Running Fleet Server:** You must have a Fleet Server instance already set up and enrolled. The Ubuntu agent will connect to this Fleet Server.
4.  **Credentials:** You need a valid **Enrollment Token** generated from Kibana.


### **Installation Method: Using Fleet in Kibana**

This is the simplest and most reliable method, as it provides you with a ready-to-use command.

#### **Step 1: Generate the Installation Command in Kibana**


1.  **Log in to Kibana** (e.g., `http://172.16.193.153:5601`).
2.  Navigate to **Management > Fleet**.
3.  Go to the **Agent Policies** tab. You should see your newly created `ssh-server-policy`.
4.  Now, go to the **Agents** tab and click **Add agent**.
5.  **Select your agent policy:** From the dropdown, choose your new **`ssh-server-policy`**.
6.  **Add the Fleet Server host:** that we had created already.
7.  **Select the Operating System:** Choose **Linux**.
8.  **Copy the Install Command:** A large `curl`-based command will appear in the box. **Copy this entire command.**

Your screen should look similar to this:

```bash
sudo curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.13.0-linux-x86_64.tar.gz
sudo tar xzvf elastic-agent-8.13.0-linux-x86_64.tar.gz
cd elastic-agent-8.13.0-linux-x86_64
sudo ./elastic-agent install --url=https://172.16.193.154:8220 --enrollment-token=SFMyNTY...
```
**Important:** Add --insecure at the end of the command. (because the command will use self-signed certificates, test purposes).
**Copy this entire command.**

#### **Step 2: Run the Command on Your Ubuntu Server**

1.  **SSH into your Ubuntu server**.
2.  **Paste and execute the entire command** you copied from Kibana.
3.  The script will:
    *   Download the Elastic Agent.
    *   Extract it.
    *   Run the installation command with the `--url` and `--enrollment-token` parameters, which tell the agent how to find your Fleet Server and authenticate.

#### **Step 3: Verify Enrollment in Kibana**

1.  Go back to **Kibana > Management > Fleet > Agents**.
2.  Within 30 seconds, you should see a new agent enrolled with a **Healthy** status. The hostname will match your Ubuntu server's hostname.



### **Managing the Elastic Agent Service**

Once installed, the Elastic Agent runs as a system service.

*   **Check status:**
    ```bash
    sudo systemctl status elastic-agent
    ```
*   **Stop the agent:**
    ```bash
    sudo systemctl stop elastic-agent
    ```
*   **Start the agent:**
    ```bash
    sudo systemctl start elastic-agent
    ```
*   **Restart the agent** (useful after policy changes):
    ```bash
    sudo systemctl restart elastic-agent
    ```
*   **View logs** (for troubleshooting):
    ```bash
    sudo tail -f /opt/Elastic/Agent/data/elastic-agent.log
    ```


### **Verification in Kibana Discover**

1.  **Go to Discover:** In Kibana, navigate to **Analytics > Discover**.
2.  **Select the Data View:** Ensure the `logs-*` data view is selected.
3.  **Search for Your Agent:** In the search bar, use the KQL (Kibana Query Language) to find logs specifically from your new server:
    ```
    agent.name : "ssh-server"
    ```
    *(Replace `ssh-server` with the actual hostname of your Ubuntu server if it's different)*

4.  **Analyze the Results:** You should see a list of log events flowing in from your Ubuntu server.
    *   You can expand any event to see all the detailed fields.
    *   Look for fields like `message`, `log.level`, `host.name`, and `agent.name` to verify the data is coming from the correct source.

    

### **What This Proves:**

*   **Successful Communication:** The Elastic Agent on your Ubuntu server is successfully communicating with the Fleet Server.
*   **Policy Applied:** The agent has received its policy from Fleet.
*   **Data Ingestion:** The agent is collecting system data (logs and metrics) and sending it to Elasticsearch.
*   **Indexing:** Elasticsearch is receiving the data and indexing it correctly.
*   **Searchability:** Kibana can successfully query and display the data.

This is the definitive proof that your entire pipeline—from the Ubuntu host to the Fleet Server to the Elastic Stack—is configured correctly. Well done!


### **Troubleshooting Common Issues**

*   **"Connection failed" errors:** The agent cannot reach the Fleet Server.
    *   **Check:** Run `curl -v https://<fleet-server-ip>:8220` from your Ubuntu host. If it fails, check firewalls and network connectivity.
*   **"Invalid enrollment token":** The token has expired or is incorrect.
    *   **Fix:** Generate a new enrollment token from the correct agent policy in Kibana and reinstall.
*   **Agent is "Unhealthy" in Fleet:**
    *   **Check Logs:** The number one source of truth. Use `sudo tail -f /opt/Elastic/Agent/data/elastic-agent.log`.
    *   **Check Policy:** Ensure the agent policy assigned to the host has valid integrations/outputs.
