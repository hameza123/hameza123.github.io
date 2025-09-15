+++ 
draft = false
date = 2025-09-13T18:15:23+01:00
title = "Day 22: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 22"
slug = "SOC Analyst Challenge, Day 22"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++


## Day 22: How To Create Alerts and Dashboards in Kibana
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

 
 
### **Part 1: Creating the Alerts**

We will create three individual alert rules based on your queries.

#### **Alert 1: Suspicious Apollo Process Creation**

1.  Go to **Management > Observability > Alerting > Rules**.
2.  Click **Create rule**.
3.  **Rule name:** `Alert: Suspicious Apollo Execution`
4.  **Rule type:** `Elasticsearch query`.
5.  **Index:** `logs-*` or `winlogbeat-*`.
6.  **Query (KQL):**
    ```kql
    event.code: "1" and event.provider: "Microsoft-Windows-Sysmon" and winlog.event_data.OriginalFileName: "Appollo.exe"
    ```
7.  **Time window:** `5m` (Run every 5 minutes, check last 5 minutes of data).
8.  **Trigger condition:** `Number of documents` ; `is above` ; `0`.
9.  **Add Action:** Configure an email, Slack message, or log to an index. Use a message like:
    ```
    Alert: Apollo Agent Executed on {{context.hits.0.winlog.computer_name}}.
    User: {{context.hits.0.winlog.event_data.User}}
    Command Line: {{context.hits.0.winlog.event_data.CommandLine}}
    ```
10. **Click Save.**

#### **Alert 2: Process Creation from PowerShell or CMD**

1.  **Create a new rule.**
2.  **Rule name:** `Alert: Process from Shell Parent`
3.  **Rule type:** `Elasticsearch query`.
4.  **Query (KQL):**
    ```kql
    event.code: 1 and event.provider: "Microsoft-Windows-Sysmon" and
    (winlog.event_data.ParentImage: "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe" or
     winlog.event_data.ParentImage: "C:\\Windows\\system32\\cmd.exe")
    ```
    *Note: Using `ParentImage` is often more reliable than `ParentCommandLine` for this specific case.*
5.  **Time window:** `5m`.
6.  **Trigger condition:** `Number of documents` ; `is above` ; `5` (Adjust based on your environment's noise).
7.  **Group over:** `winlog.computer_name` (Get separate alerts per host).
8.  **Add Action** and **Save.**

#### **Alert 3: Windows Defender Antivirus Disabled**

1.  **Create a new rule.**
2.  **Rule name:** `Alert: Windows Defender Disabled`
3.  **Rule type:** `Elasticsearch query`.
4.  **Query (KQL):**
    ```kql
    event.code: 5001 and event.provider: "Microsoft-Windows-Windows Defender"
    ```
5.  **Time window:** `5m`.
6.  **Trigger condition:** `Number of documents` ; `is above` ; `0`.
7.  **Add Action** and **Save.**

---

### **Part 2: Building the Dashboard**

Now, let's create visualizations for your dashboard.

#### **Visualization 1: Apollo Execution Events**

1.  Go to **Analytics > Dashboard > Create dashboard**.
2.  Click **Create visualization**.
3.  Choose **Lens**.
4.  Select your Windows data view (e.g., `logs-*`).
5.  **Configure:**
    *   **Vertical axis:** `Count of records`.
    *   **Horizontal axis:** `@timestamp` (with a `1d` interval for a trend line).
    *   **Breakdown by:** `winlog.computer_name`.
    *   **Add a filter:**
        ```kql
        event.code: "1" and event.provider: "Microsoft-Windows-Sysmon" and winlog.event_data.OriginalFileName: "Appollo.exe"
        ```
6.  Click **Save and return**. Name it: `Apollo Executions Over Time`.

#### **Visualization 2: Top Parents for Sysmon Event ID 1**

1.  **Create new visualization**, choose **Data Table**.
2.  Select your data view.
3.  **Metrics:** `Count`.
4.  **Buckets > Split rows:**
    *   **Aggregation:** `Terms`.
    *   **Field:** `winlog.event_data.ParentImage`.
    *   **Size:** `10`.
5.  **Add a filter:**
    ```kql
    event.code: 1 and event.provider: "Microsoft-Windows-Sysmon"
    ```
6.  **Save** as `Top Parent Processes for Process Creation`.

#### **Visualization 3: Sysmon Network Connections (Initiated)**

1.  **Create new visualization**, choose **Lens**.
2.  Select your data view.
3.  **Configure:**
    *   **Vertical axis:** `Count of records`.
    *   **Horizontal axis:** `winlog.event_data.DestinationIp`.
    *   **Breakdown by:** `winlog.event_data.Image`.
    *   **Add a filter:**
        ```kql
        event.code: 3 and event.provider: "Microsoft-Windows-Sysmon" and winlog.event_data.Initiated: "true"
        ```
4.  **Save** as `Outbound Network Connections by Destination IP`.

#### **Visualization 4: Windows Defender 5001 Events**

1.  **Create new visualization**, choose **Data Table**.
2.  Select your data view.
3.  **Metrics:** `Count`.
4.  **Buckets > Split rows:**
    *   **Aggregation:** `Terms`.
    *   **Field:** `host.hostname`.
    *   **Size:** `10`.
5.  **Add a filter:**
    ```kql
    event.code: 5001 and event.provider: "Microsoft-Windows-Windows Defender"
    ```
6.  **Save** as `Hosts with Defender Disabled`.

---

### **Part 3: Assembling the Dashboard**

1.  Go back to your dashboard.
2.  Click **Edit > Add**.
3.  Select all the visualizations you just created:
    *   `Apollo Executions Over Time`
    *   `Top Parent Processes for Process Creation`
    *   `Outbound Network Connections by Destination IP`
    *   `Hosts with Defender Disabled`
4.  **Arrange the panels** logically. For example:
    *   Top row: `Apollo Executions` and `Defender Disabled` (your high-priority alerts).
    *   Bottom row: `Top Parent Processes` and `Network Connections` (trending data).
5.  **Set a time filter** at the top (e.g., "Last 24 hours").
6.  **Click Save** to save the dashboard. Name it `Windows Endpoint Security Overview`.
 
 
This dashboard and alert setup will give you a powerful, real-time overview of critical security events on your Windows endpoints, directly based on the precise queries you provided.

