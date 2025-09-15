+++ 
draft = false
date = 2025-09-10T18:10:32+01:00
title = "Day 8: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 8 "
slug = "SOC Analyst Challenge, Day 8 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC","Sysmon"]
series = []
+++


## Day 8: What is Sysmon?
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)


**Sysmon** (System Monitor) is a free, lightweight **system service** and **device driver** for Microsoft Windows, created by Microsoft. It's part of the Windows Sysinternals suite of tools.

Its primary purpose is not to *prevent* attacks but to provide incredibly **detailed, high-quality logs** about system activity. Think of it as a surveillance camera for your Windows operating system.


### **Core Purpose: "What happened on this machine?"**

While Windows Event Log provides basic information, Sysmon gives security teams and system administrators **deep, forensic-level visibility** into what's happening on a system. It answers critical questions that native logs often can't.

#### **What Makes Sysmon Powerful (Its Key Features):**

1.  **Detailed Process Tracking:**
    *   Logs process creation with full command-line arguments.
    *   Records the parent process that spawned each new process.
    *   Hashes process images (MD5, SHA1, SHA256, IMPHASH).
    *   This is crucial for spotting suspicious chains of activity (e.g., `powershell.exe` spawned by an unknown script).

2.  **Network Connection Monitoring:**
    *   Logs all TCP and UDP connections made by processes.
    *   Records source and destination IP addresses, ports, and the process responsible.
    *   This helps identify unauthorized data exfiltration or command-and-control (C2) callbacks.

3.  **File and Registry Change Auditing:**
    *   Can monitor when specific files or registry keys are created, modified, or deleted.
    *   Extremely useful for detecting persistence mechanisms (e.g., malware adding a Run key).

4.  **Other Key Events:** It also logs details for:
    *   **Driver and DLL loading.**
    *   **Named pipe creation.**
    *   **WMI permanent event subscriptions.**
    *   **Raw disk access.**

 

### **How Does It Work?**

1.  **Installation:** You download and install Sysmon on a Windows machine. It runs as a service and loads a driver to capture low-level system events.
2.  **Configuration:** This is the most important part. By default, Sysmon logs *everything*, which is far too noisy. You use an **XML configuration file** to tell Sysmon exactly what events to log and, more importantly, what to **ignore**. Renowned security researchers like **SwiftOnSecurity** and **Olaf Hartong** have created excellent open-source configs that filter out normal noise and highlight potentially malicious activity.
3.  **Log Generation:** Sysmon writes its logs to the **Windows Event Viewer** under `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`.
4.  **Collection & Analysis:** The logs are then collected by a SIEM (like Elasticsearch via Elastic Agent) where they can be correlated, analyzed, and alerted upon.

{{<mermaid>}}
flowchart LR
A[Sysmon Installation<br>on Windows System] --> B{Configure with<br>XML Filter Rules};
B --> C[Logs System Activity<br>Process, Network, File, Registry];
C --> D[Writes Events to<br>Windows Event Log];
D --> E[Elastic Agent<br>Collects Events];
E --> F[Sent to SIEM<br>e.g., Elastic Stack];
F --> G[Analysis, Alerting,<br>& Forensics];
{{</mermaid>}}

### **Why is Sysmon a Critical Security Tool?**

*   **Visibility:** It provides an unparalleled level of detail for investigating security incidents.
*   **Detection:** It dramatically improves the ability to detect advanced threats and lateral movement within a network that would otherwise go unnoticed.
*   **Forensics:** After a breach, Sysmon logs are invaluable for understanding the "how" and "what" of an attack, allowing you to trace the attacker's steps.
*   **Free and Lightweight:** It has a minimal performance impact and is completely free to use.

**In summary: Sysmon is the de facto standard for enhancing visibility on Windows endpoints. It turns the typically noisy Windows Event Log into a powerful, targeted source of security intelligence.** It is a foundational component of any mature security operations program.