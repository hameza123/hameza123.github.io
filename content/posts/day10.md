+++ 
draft = false
date = 2025-09-10T20:09:49+01:00
title = "Elasticsearch Ingest Data Tutorial, Day 10 "
description = "SOC Analyst Challenge, Day 10 "
slug = "SOC Analyst Challenge, Day 10 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC","Sysmon"]
series = []
+++


## Day 10: Elasticsearch Ingest Data Tutorial 
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

This tutorial covers how to ingest specific Windows Event Logs (Sysmon and filtered Windows Defender) using Elastic Agent integrations.

#### **Part 1: Add the Custom Log Integration for Sysmon**

1.  **Navigate to Integrations:** In Kibana (`http://172.16.193.153:5601`), go to **Management > Integrations**.

2.  **Browse for Integration:** In the search bar, type **"costum windows event logs"**.

3.  **Add Custom Logs:**
    *   On the Custom Logs integration page, click **Add costum windows event logs**.
    *   Give the integration a descriptive name (e.g., `Sysmon Custom Collection`), and a description.

4.  **Configure Channel Name:**

    *	**Open Event Viewer:**
		    *   Press `Win + R`, type `eventvwr.msc`, and press Enter.

	*	**Navigate to the Sysmon Log:**
		    *   In the left-hand pane, expand **Applications and Services Logs** > **Microsoft** > **Windows**.
		    *   Scroll down and find the folder named **Sysmon**.
		    *   Click on it, then select the **Operational** log.

	*	**Copy the Full Log Name:**
		    *   In the **Actions** pane on the right, click on **Properties** for the "Operational" log.
		    *   In the Properties window, the **Full Name** field contains the exact string you need to use.
		    *   **Select and copy** this entire text.
        

5.  **Select the Agent Policy you assigned to your Windows server (e.g., `windows-servers`).**

6.  **Save and Deploy:** Click **Save and continue**. The new integration will be added to your agent policy. The Elastic Agent on your Windows server will automatically receive the updated policy within a few minutes.

#### **Part 2: Add Windows Defender Integration with Specific Event IDs**

1.  **Browse for Integration:** Go back to **Integrations** and search for **"Add costum windows event logs"**.
2.  **Add Integration:** Click on **Add costum windows event logs**.
3.  **Add Custom Logs:**
    *   On the Custom Logs integration page, click **Add costum windows event logs**.
    *   Give the integration a descriptive name (e.g., `defender Custom Collection`), and a description.

4.  **Configure Channel Name:**

	*	**Open Event Viewer:**
		    *   Press `Win + R`, type `eventvwr.msc`, and press Enter.

	*	**Navigate to the Sysmon Log:**
		    *   In the left-hand pane, expand **Applications and Services Logs** > **Microsoft** > **Windows**.
		    *   Scroll down and find the folder named **windows defender**.
		    *   Click on it, then select the **Operational** log.

	*	**Copy the Full Log Name:**
		    *   In the **Actions** pane on the right, click on **Properties** for the "Operational" log.
		    *   In the Properties window, the **Full Name** field contains the exact string you need to use.
		    *   **Select and copy** this entire text.
        

5. On **Advanced options** add **1116,1117,5001** on **Event ID**, we dont need all the noise made by defender so we will add just this three logs.

	**What These Event IDs Mean:**

	*   **Event ID 1116:** **Malware detected but not remediated.** This is critical for identifying active threats that Defender could not automatically remove, requiring manual intervention.
	*   **Event ID 1117:** **Malware successfully remediated.** Important for confirmation that a threat was cleaned and for tracking infection rates.
	*   **Event ID 5001:** **Antivirus protection was turned off.** This is a huge red flag for potential malicious activity, as attackers often disable security software.


6.  **Select the Agent Policy you assigned to your Windows server (e.g., `windows-servers`).**
7.  **Save and Deploy:** Click **Save and continue**.

#### **Part 3: Restart the Elastic Agent (Best Practice)**

To ensure the new configuration is loaded cleanly, restart the Elastic Agent service on your Windows Server 2022.

1.  Open **PowerShell as an Administrator**.
2.  Run the restart command:
    ```powershell
    Restart-Service -Name ElasticAgent
    ```
3.  Verify the service is running again:
    ```powershell
    Get-Service -Name ElasticAgent
    ```

#### **Part 4: Verify Data in Discover**

This is the crucial step to confirm everything is working.

1.  In Kibana, go to **Analytics > Discover**.
2.  **Select the correct data view:** Use the dropdown to select `logs-*`.
3.  **Search for Sysmon Events:** In the search bar, use the KQL (Kibana Query Language) to find the classic Sysmon Process Creation event:
    ```
    winlog.event_id : 1
    ```
    *   You should see a list of events.
    *   In the results, look for the field `winlog.provider`. For Sysmon events, this will be **`Microsoft-Windows-Sysmon`**.
    *   Expand an event to see the rich, parsed data in the `winlog.event_data` field (e.g., `Image`, `CommandLine`, `ParentImage`).

4.  **Search for Defender Events:** Now, search for one of your specific Defender events:
    ```
    winlog.event_id : (1116 or 1117 or 5001) 
    ```
    *   You might see results for these specific events (but not a must until its generated). The `winlog.provider` for these will be **`Microsoft-Windows-Windows Defender`** or similar.

---

### **What You Have Achieved:**

*   **Targeted Data Collection:** You are no longer collecting all Windows events, only the most valuable security events from Sysmon and a curated list from Windows Defender.
*   **Structured Parsing:** By using the **Windows Event Log** processor, you have automatically parsed complex XML into searchable, filterable fields in Elasticsearch. This is essential for building effective dashboards and alerts.
*   **Optimized Performance:** Filtering at the collection level (e.g., specific Event IDs) reduces the volume of data ingested, saving storage and improving performance.

Your setup is now perfectly configured for advanced security monitoring and threat hunting. The next step is to use this data to create **detection rules** in Elastic Security and build **dashboards** in Kibana.



