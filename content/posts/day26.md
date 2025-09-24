+++ 
draft = false
date = 2025-09-13T18:15:33+01:00
title = "Investigate SSH Brute Force Attack, Day 26 "
description = "SOC Analyst Challenge, Day 26"
slug = "SOC Analyst Challenge, Day 26"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++

## Day 26: Investigate SSH Brute Force Attack
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

### **Investigation Guide: SSH Brute Force Attack in Kibana**

This process involves using Kibana's Discover app and its powerful KQL (Kibana Query Language) to find evidence of the attack, identify the source, and understand its scope.

 
### **1. Find the Evidence in Discover**

First, you need to locate the failed login attempts.

1.  **Go to Discover:** Navigate to **Analytics > Discover**.
2.  **Select the Correct Data View:** Ensure you have the right data view selected (e.g., `logs-*` or `logs-ssh*`).
3.  **Apply a Time Filter:** Set the time range to the last 24 hours or when you suspect the attack occurred.

#### **Key Search Queries (KQL):**

*   **Find All Failed SSH Login Attempts:**
    ```kql
    system.auth.ssh.event : "Failed" 
    ```
    or, if the field is different:
    ```kql
    message: "Invalid user"
    ```

*   **Find Authentication Failures from a Specific IP:** (Replace `172.16.193.160` with the IP you found)
    ```kql
    source.ip: 172.16.193.160 and message: "Invalid user"
    ```


 

### **2. Analyze the Attack Pattern**

Once you have the results, use Kibana's features to analyze the pattern.

1.  **Visualize the Volume:** Click the **Auto-refresh** button to see if events are still pouring in live.
2.  **Identify the Top Attacker IP:**
    *   In the **Available fields** list on the left, find `source.ip`.
    *   Click the **Visualize** lens icon next to it. This will show you a bar chart of the top IPs by number of events.
    *   The IP with the highest count is your likely attacker.
3.  **See Targeted Usernames:** Do the same for the `user.name` field. This will show you which usernames the attacker is trying to guess.
4.  **Check the Timeline:** Use the `@timestamp` field to see if the attack is ongoing or happened in a specific burst.

    

 

### **3. Create a Detection Visualization / Dashboard**

For ongoing monitoring, create a visualization to track this.

1.  Click **Create visualization** and choose **Lens**.
2.  **Configure the Visualization:**
    *   **Vertical axis:** `Count of records` (this will be the number of failed attempts).
    *   **Horizontal axis:** `@timestamp` (with an interval like `1 hour` or `30 minutes`).
    *   **Breakdown by:** `source.ip` (to color-code the attacks by source IP).
3.  Add a filter to only show failed logins:
    ```kql
    system.auth.ssh.event : "Failed" 
    ```
4.  Save this visualization with a name like `SSH Failed Logins Over Time`.

 

### **4. (Crucial) Check for Success**

The goal of a brute force attack is to eventually succeed. You must check if the attacker guessed a password.

1.  **Search for Successful Logins from the Attacker's IP:**
    ```kql
    source.ip: "172.16.193.160" and system.auth.ssh.event : "Accepted"  
    ```
2.  **Search for Successful Logins for Targeted Users:** Look for successful logins for the usernames that were being brute-forced, especially from any IP, not just the attacker's. The attacker may have succeeded and then logged in from a different machine.
    ```kql
    user.name: "root" and system.auth.ssh.event : "Accepted"  
    ```

 
### **5. Triage & Response Actions**

If you confirm a successful login, act immediately.

1.  **Contain:** Immediately block the attacker's IP address at the firewall.
    *   On Ubuntu: `sudo ufw deny from 172.16.193.160`
2.  **Identify:** Determine which server was compromised from the `host.name` or `host.ip` field in the successful login event.
3.  **Investigate:** On the compromised server:
    *   Check the command history for the compromised user: `cat ~/.bash_history`.
    *   Look for unknown processes: `ps auxf`.
    *   Check for unauthorized SSH keys: `cat ~/.ssh/authorized_keys`.
4.  **Eradicate:** Reset the password for the compromised user account. Rotate any SSH keys.

 

### **6. Proactive Defense: Create an Alert**

Automate the detection for next time.

1.  Go to **Management > Observability > Alerting > Rules** and create a new rule.
2.  **Rule type:** `Elasticsearch query`.
3.  **Index:** `logs-*`
4.  **Query (KQL):**
    ```kql
    system.auth.ssh.event : "Failed"  
    ```
5.  **Trigger condition:** `Number of documents` ; `is above` ; `50` ; `over the last` ; `5 minutes`.
6.  **Group by:** `source.ip` (This creates a separate alert for each attacking IP).
7.  **Add an Action:** Configure the Osticket server.
### **Summary of Investigation Queries**

| Purpose | KQL Query |
| :--- | :--- |
| **Find Failed Attempts** | `system.auth.ssh.event : "Failed" ` |
| **Find Attacks from a Specific IP** | `source.ip: "172.16.193.160" and system.auth.ssh.event : "Failed"`  |
| **Check for "Invalid User"** | `message: "Invalid user`  |
| **Check for Attacker's Success** | `source.ip: "172.16.193.160" and system.auth.ssh.event : "Accepted"`  |
| **Check for Any Success on Targeted Users** | `user.name: (root) and esystem.auth.ssh.event : "Accepted"`  |

By following this process, you can move from detecting an attack, to analyzing its scope, to responding to it, and finally to automating its detection in the future.