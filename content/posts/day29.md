+++ 
draft = false
date = 2025-09-13T18:15:41+01:00
title = "Day 29:  Elastic Defend Setup Tutorial  "
description = "SOC Analyst Challenge, Day 29"
slug = "SOC Analyst Challenge, Day 29"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++

## Day 29: Elastic Defend Setup Tutorial  
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)


### **What is Elastic Defend?**

Elastic Defend is a security integration that transforms the standard Elastic Agent into a powerful EDR tool. It provides:
*   **Advanced Threat Prevention:** Blocks malicious processes and behaviors.
*   **Endpoint Visibility:** Detailed process, file, network, and registry auditing.
*   **Detection & Response:** Built-in detection rules for common attack techniques.

### **Prerequisites**
*   A running **Elastic Stack** (Elasticsearch & Kibana) version 8.0+.
*   A **Fleet Server** already set up and running.
*   Administrator access on the endpoints you want to protect (Windows, macOS, or Linux).

### **Setup Tutorial**

#### **Part 1: Enable and Configure Elastic Defend in Kibana**

1.  **Access Fleet:**
    *   Log in to Kibana.
    *   Navigate to **Management > Integrations**.

2.  **Add the Elastic Defend Integration:**
    *   In the Fleet tab, go to **Integrations**.
    *   Search for **"Elastic Defend"**.
    *   Click on the integration and then click **Add Elastic Defend**.

3.  **Configure the Integration:**
    *   **Select a policy:** Choose an existing agent policy (e.g., `your-win-server`) or create a new one (e.g., `edr-windows-policy`).
    *   **Configuration options:** This is where you set the security level.
    *   Click **Save and continue**.

    

#### **Part 2: Verify Data Ingestion and Explore Security Features**

1.  **Check for Data:**
    *   Go to **Analytics > Discover**.
    *   Select the `logs-*` data view.
    *   Search for data from your endpoint:
        ```kql
        agent.name : "your-windows-hostname" and event.module : "endpoint"
        ```
    *   You should see process creation, network events, and file events flowing in.

2.  **Explore the Security App:**
    *   Navigate to **Security > Hosts**.
    *   You should see your endpoint listed. Click on it to see a detailed overview of its security posture, including running processes, events, and potential alerts.

### **Part 3: Key Features and How to Use Them**

#### **1. Hosts Overview (Security > manage > Endpoints)**
*   View all endpoints enrolled in Elastic Defend.
*   See their risk scores, last seen time, and operating system.
*   Click on any host to investigate its activity.

#### **2. Alerts (Security > Alerts)**
*   This is where Elastic Defend's built-in detection rules display potential threats.
*   Alerts are automatically generated based on malicious behavior (e.g., ransomware, suspicious process injection, etc.).

#### **3. Timeline (Security > Timeline)**
*   This is your investigation workspace.
*   You can create new timelines to manually investigate events or open existing ones from alerts.
*   Correlate process, network, and file events to understand the full attack chain.

#### **4. Management (Security > Management)**
*   **Trusted Applications:** Define processes that are allowed to run to avoid false positives.
*   **Event Filters:** Exclude noisy but benign events from generating alerts.
*   **Blocklist:** Define specific processes, hashes, or URLs that should always be blocked.

#### **5. Live Terminal (Interactive Response)**
*   For advanced users, Elastic Defend allows you to open a live shell on the endpoint directly from Kibana.
*   **Access:** Go to **Security > manage > action and click**… > **Respond**.
*   **This allows you to run commands** like `ps` (Linux/macOS) or `tasklist` (Windows) to investigate in real-time.

### **Example Investigation: Looking for a Suspicious Process**

1.  **Go to Security > Hosts** and select your endpoint.
2.  In the "Events" section, filter for `event.action: "process_started"`.
3.  Look for unfamiliar processes, especially those with unusual parent processes or running from temporary folders (`C:\Users\*\AppData\Local\Temp\`).
4.  If you find something, click on the event and select **"Take action"** > **"Add to blocklist"** to prevent it from ever running again.

### **Troubleshooting**

*   **No data in Discover?** Check the agent's status in Fleet. Ensure the policy has the Elastic Defend integration.
*   **"Unauthorized" errors?** The agent likely doesn't have the correct permissions. Ensure it was installed as Administrator/root.
*   **Performance issues?** The `Prevent` mode uses more resources than `Detect` mode. Adjust your policy settings.

By following these steps, you have deployed a modern, powerful EDR solution that provides deep visibility and protection for your endpoints, all centrally managed from your Kibana dashboard.