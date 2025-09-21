+++ 
draft = false
date = 2025-09-13T18:15:35+01:00
title = "Day 27: Investigate RDP Brute Force Attack"
description = "SOC Analyst Challenge, Day 27"
slug = "SOC Analyst Challenge, Day 27"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++

## Day 26: Investigate RDP Brute Force Attack
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

 

### **Investigation Guide: RDP Brute Force Attack in Kibana**

The primary data source will be Windows Event Logs (specifically Security logs) forwarded by the Elastic Agent.

 

### **1. Find the Evidence in Discover**

The key is to search for failed logon attempts on the port used by RDP.

1.  **Go to Discover:** Navigate to **Analytics > Discover**.
2.  **Select the Correct Data View:** Use `logs-*` or `logs-windows.security*`.
3.  **Apply a Time Filter:** Set the time range to the last 24 hours or the suspected attack window.

#### **Key Search Queries (KQL):**

*   **Find All Failed RDP Logon Attempts:** This is the most important query. It looks for failed logins with the logon type for RDP.
    ```kql
    event.code: "4625" and winlog.event_data.LogonType: "10"
    ```
    *   `event.code: "4625"` is the Windows Event ID for a failed logon.
    *   `winlog.event_data.LogonType: "10"` specifically defines a remote interactive logon (RDP).

*   **Find Failed Logons from a Specific Attacker IP:**
    ```kql
    event.code: "4625" and winlog.event_data.LogonType: "10" and source.ip: "172.16.193.160"
    ```

*   **Find Failed Logons for Specific Targeted Accounts:** Attackers often target common admin accounts.
    ```kql
    event.code: "4625" and winlog.event_data.LogonType: "10" and user.name: ("Administrator")
    ```

 

### **2. Analyze the Attack Pattern**

Use Kibana's features to understand the scope and scale of the attack.

1.  **Identify the Top Attacker IPs:**
    *   In the **Available fields** list, find `source.ip`.
    *   Click the **Visualize** lens icon next to it. The IP with the highest count is the likely attacker.
2.  **See Targeted Usernames:** Do the same for the `user.name` field. This shows which accounts are under attack.
3.  **Identify the Targeted Host:** Check the `host.name` field. Which machine is being attacked?
4.  **Timeline of the Attack:** Use the `@timestamp` field to see the duration and intensity of the attack. Was it a short burst or a long, slow campaign?

    

 

### **3. Create a Detection Visualization / Dashboard**

Build a visual to monitor RDP attacks in real-time.

1.  Click **Create visualization** and choose **Lens**.
2.  **Configure the Visualization:**
    *   **Vertical axis:** `Count of records` (number of failed RDP attempts).
    *   **Horizontal axis:** `@timestamp` (with an interval like `1 hour`).
    *   **Breakdown by:** `source.ip` (to see attacks by source IP).
3.  Add a filter to only show failed RDP logons:
    ```kql
    event.code: "4625" and winlog.event_data.LogonType: "10"
    ```
4.  Save this as `RDP Failed Logons Over Time`.

 
### **4. (Crucial) Check for Success**

The ultimate goal is to see if the attacker was successful.

1.  **Search for Successful RDP Logons from the Attacker's IP:**
    ```kql
    event.code: "4624" and winlog.event_data.LogonType: "10" and source.ip: "172.16.193.160"
    ```
    *   `event.code: "4624"` is the Windows Event ID for a successful logon.

2.  **Search for Any Successful RDP Logons for Targeted Users:** The attacker may have succeeded and then logged in from a different IP.
    ```kql
    event.code: "4624" and winlog.event_data.LogonType: "10" and user.name: "Administrator"
    ```

3.  **Correlate with Network Data:** Look for network connections (Sysmon Event ID 3) to the RDP port around the time of the successful login.
    ```kql
    event.code: "3" and destination.port: "3389"
    ```

 

### **5. Triage & Response Actions**

If you find a successful login, act immediately.

1.  **Contain:**
    *   **Block the IP:** Immediately block the attacker's IP address at the network firewall.
    *   **Disable RDP:** If possible, temporarily disable RDP on the compromised host until it's secured.
2.  **Identify:** The `host.name` field in the events will tell you which machine was compromised.
3.  **Investigate on the Host:**
    *   Check logged-on users: `Get-NetTCPConnection -LocalPort 3389` or `query user`.
    *   Review recent logons: In Event Viewer, look under **Windows Logs > Security** for recent 4624 events.
    *   Check for suspicious processes and persistence mechanisms.
4.  **Eradicate:**
    *   **Reset Passwords:** Force a password reset for the compromised user account.
    *   **Rotate Credentials:** If the same account is used elsewhere, rotate those passwords too.

 

### **6. Proactive Defense: Create an Alert**

Automate detection to catch the next attack faster.

1.  Go to **Management > Observability > Alerting > Rules** and create a new rule.
2.  **Rule type:** `Elasticsearch query`.
3.  **Index:** `logs-*`
4.  **Query (KQL):**
    ```kql
    event.code: 4625 and winlog.event_data.LogonType: 10
    ```
5.  **Trigger condition:** `Number of documents` ; `is above` ; `10` ; `over the last` ; `5 minutes`.
6.  **Group by:** `source.ip` and `host.name` (This creates a separate alert for each attacker/target pair).
7.  **Add an Action:** Configure OsTicket server.

### **Summary of Investigation Queries**

| Purpose | KQL Query |
| :--- | :--- |
| **Find Failed RDP Attempts** | `event.code: "4625" and winlog.event_data.LogonType: "10"` |
| **Find Attacks from a Specific IP** | `event.code: "4625" and winlog.event_data.LogonType: "10" and source.ip: "172.16.193.160"` |
| **Check for Attacker's Success** | `event.code: "4624" and winlog.event_data.LogonType: "10" and source.ip: "172.16.193.160"` |
| **Check for Any RDP Success** | `event.code: "4624" and winlog.event_data.LogonType: "10"` |
| **Find Network Connections to RDP** | `event.code: "3" and  destination.port: "3389"` |

By following this process, you can effectively detect, investigate, and respond to RDP brute force attacks using the powerful correlation and visualization capabilities of Kibana.