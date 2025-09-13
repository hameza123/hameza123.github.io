+++ 
draft = false
date = 2025-09-11T09:35:21+01:00
title = "Day 14: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 14 "
slug = "SOC Analyst Challenge, Day 14 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", ""]
categories = ["SOC",""]
series = []
+++


## Day 14: How To Create Alerts and Dashboards in Kibana
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

### **Part 1: Creating Alerts (Kibana Alerting)**

Alerts automatically notify you when specific conditions are met in your data.

#### **Scenario: Alert on Failed SSH Login Attempts**

Let's create an alert that triggers after 5 failed SSH attempts from a single source IP within 5 minutes.

1.  **Navigate to Alerting:**
    *   In Kibana, go to **Management > Observability > Alerting**.
    *   Click on the **Rules** tab and then **Create rule**.

2.  **Define the Rule:**
    *   **Rule name:** `Alert: Multiple Failed SSH Logins`
    *   **Rule type:** Select **Elsearch query**. This is the most common type.

3.  **Define the Query (The Condition):**
    *   **Index:** Select your SSH data index pattern (e.g., `logs-ssh*`).
    *   **Query DSL (KQL):** Write a query to find failed SSH attempts.
        ```kql
        event.action: "failed-login" or message: "Failed password"
        ```
    *   **Time window:** Set this to **5 minutes**. The rule will run every 5 mins and check the last 5 mins of data.

4.  **Set the Trigger Condition:**
    *   This defines *when* the alert fires.
    *   Set **Alert me when:** `Number of documents` ; **is above** ; `5`.
    *   **Group over:** `source.ip` (This is the key! It creates a separate alert *for each unique source IP* that meets the condition).

    

5.  **Add Actions (The Notification):**
    *   Click **Add action**.
    *   **Action type:** Choose how you want to be notified (e.g., **Email**, **Slack**, **Microsoft Teams**, **Index**).
    *   **Configure the action:**
        *   For **Email:** Fill in the recipient(s) and a subject/message. You can use variables like `{{context.message}}`.
        *   For **Index:** This is very powerful. It writes the alert data to a new Elasticsearch index, creating a permanent audit log of all alerts. You can call the index `.alerts-ssh-bruteforce`.
    *   **Message example for email:**
        ```
        Alert: Possible SSH Brute Force Attack!
        IP Address {{context.group}} has generated {{context.value}} failed login attempts in the last 5 minutes.
        ```

6.  **Review and Save:** Click **Save** to activate the rule.

Your alert is now live! Kibana will run this check every 5 minutes and notify you via the action you configured whenever the condition is met.

---

### **Part 2: Creating Dashboards**

Dashboards are visual summaries of your data, built from individual visualizations.

#### **Scenario: Create a Security Overview Dashboard**

1.  **Navigate to Dashboards:**
    *   Go to **Analytics > Dashboards**.
    *   Click **Create dashboard**.

2.  **Create Visualizations for Your Dashboard:**
    You'll create them one by one and add them to the dashboard.

    **a. A Lens Chart: SSH Logins Over Time**
    *   Click **Create visualization** or **Edit** on the dashboard > **Create new**.
    *   Choose **Bar**.
    *   Select your SSH data view (e.g., `logs-ssh*`).
    *   **Drag and drop:**
        *   `@timestamp` to the **Horizontal axis**.
        *   `event.action` to the **Vertical axis**. Lens will automatically choose **Count**.
        *   `system.auth.ssh.event` to **Breakdown by**. This will split the bar chart into colors for `successful-login`, `failed-login`, etc.
    *   Click **Save and return** and name it `SSH Login Activity`.

    

    **b. A Metric: Total Failed Logins (Last 24h)**
    *   **Create new visualization**, choose **Lens**.
    *   Select your data view.
    *   Change the chart type to **Metric**.
    *   Drag `event.action` into the workspace. Add a filter: `event.action: "failed-login"`.
    *   Click **Save and return** and name it `Failed Logins (24h)`.

    **c. A Top Source IPs for Failed Logins**
    *   **Create new visualization**, choose **Data Table** (or use Lens).
    *   Select your data view.
    *   Set a filter: `event.action: "failed-login"`.
    *   For the **Aggregation**, choose **Terms** and select the field `source.ip`.
    *   For the **Size**, set to 10. Order by **metric: Count** Descending.
    *   Click **Save and return** and name it `Top IPs - Failed Logins`.

3.  **Build the Dashboard:**
    *   Go back to your blank dashboard.
    *   Click **Edit** > **Add** > **Existing**.
    *   Select all the visualizations you just created (`SSH Login Activity`, `Failed Logins (24h)`, `Top IPs - Failed Logins`) and click **Add**.
    *   Drag and resize the panels to organize them logically.

4.  **Add a Time Filter:**
    *   In the dashboard toolbar, click **Add filter**.
    *   Create a filter for the `@timestamp` field. A common choice is to set a default filter of **Last 24 hours**.
    *   This allows you to easily change the time range for the entire dashboard.

5.  **Save the Dashboard:**
    *   Click **Save** in the top toolbar and give it a name, e.g., `Security Overview - SSH`.

    

### **Key Concepts to Remember**

*   **Alerts** are built on **rules** that run on a schedule, checking a **query** with a **threshold**.
*   **Group by** (like `source.ip`) is crucial for creating precise, actionable alerts.
*   **Dashboards** are built from individual **visualizations** (Lens, Maps, Data Tables).
*   **Lens** is the most intuitive and powerful tool for building most charts.
*   Always set a **time filter** on your dashboards for context.

You now have a functioning alert system for brute-force attacks and a visual dashboard to monitor authentication activity. You can apply these exact same principles to create alerts for any other event (Windows Defender detections, Sysmon process creation, etc.) and add them to your dashboard.