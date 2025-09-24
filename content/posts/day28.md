+++ 
draft = false
date = 2025-09-13T18:15:38+01:00
title = "Investigate Mythic Agent, Day 28"
description = "SOC Analyst Challenge, Day 28"
slug = "SOC Analyst Challenge, Day 28"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++

## Day 28: Investigate Mythic Agent
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

### **Investigation Guide: Mythic C2 Activity in Kibana**

Mythic is designed to be stealthy, but no C2 framework is invisible. Its activity generates logs that can be detected with careful analysis.

 

### **1. Investigate the Agent Deployment (Initial Execution)**

The first step is finding how the agent (e.g., Apollo, Athena) was executed.

#### **Key Search Queries (KQL - Sysmon Events):**

*   **Look for Unusual Process Creation (Sysmon Event ID 1):** Focus on the parent process.
    ```kql
    event.code: 1 and not process.parent.name: ("explorer.exe" or "svchost.exe" or "services.exe" or "msiexec.exe" or "winword.exe" or "excel.exe")
    ```
    This finds processes spawned by something other than common, legitimate parents.

*   **Find Processes with No Parent Command Line: (A Big Red Flag):**
    ```kql
      event.code: 1 and winlog.event_data.ParentCommandLine: "-"
    ```

*   **Search for Specific Mythic Agent Names:** Agents often have default names.
    ```kql
       event.code: 1 and winlog.event_data.Product: ("Apollo" or "Athena" or "Poseidon" or "Sandcat" or "Mythic")
    ```

*   **Look for LOLBin Execution:** Mythic often uses living-off-the-land binaries (LOLBins) for execution.
 

### **2. Investigate Network Patterns (C2 Communication)**

This is often the most reliable detection method.

#### **Key Search Queries (KQL - Sysmon/Zeek Events):**

*   **Find Beaconing Patterns:** Look for periodic, external connections.

    *   Add a filter for `domain` if you have threat intel on C2 domains.

*   **Find Connections to Unknown Ports:** Mythic can use non-standard ports for HTTP/HTTPS.
    ```kql
    event.code: 3 and not destination.port: (80 or 443 or  53 or 21 or 25 or 110 or 143 or 993 or 995) // Common legitimate ports
    ```

*   **JA3/S Hashing (SSL Fingerprinting):** This is a powerful way to detect C2 tools.

*   **DNS Tunneling Detection:** If Mythic uses DNS over HTTPS (DoH) or other profiles.
    ```kql
    dns.question.type: "AAAA" and dns.question.name: "*.mydomain.com"
    ```

 

### **3. Investigate Post-Exploitation Activity**

Once an agent is running, it will exhibit specific behaviors.

#### **Key Search Queries (KQL):**

*   **Discovery Commands:** The first thing an operator does is explore.
    ```kql
    event.code: 1 and winlog.event_data.OriginalFileName: ("whoami.exe" or "hostname.exe" or "net.exe" or "ipconfig.exe" or "nltest.exe" or "systeminfo.exe") and winlog.event_data.ParentImage: ("C:\Windows\System32\cmd.exe" or "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe")
    ```

*   **Credential Access:** A common objective.
    ```kql
    event.code: 1 and process.name: 
      ("lsass.exe", "mimikatz.exe", "sekurlsa::logonpasswords") 
    or message: "lsass"
    ```

*   **Lateral Movement Attempts:**
    ```kql
    (event.code: 3 and winlog.wmicevent_data.DestinationPort: (445 or 3389 or 135 or 5985 or 5986)) // SMB, RDP, WMI, WinRM
    or (event.code: 1 and process.name: ("wmic.exe", "sc.exe", "psexec.exe"))
    ```

 

### **4. Pivoting and Correlation**

When you find a suspicious event, pivot to see the full scope.

1.  **Find all activity for a Process GUID:** If you find a suspicious process, use its `process.guid` to find everything it did.
    ```kql
    process.guid: "{3BB25086-E897-68CA-900B-000000000100}"
    ```

2.  **Find all activity for a Logon ID:** If you find a compromised user, find all their actions.
    ```kql
    winlog.event_data.LogonId: "0xea2f1"
    ```

3.  **Find all network connections for a Process ID:**
    ```kql
    event.code: 3 and process.pid: 4076
    ```

 

### **5. Building a Detection Dashboard**

Create a dashboard with these visualizations for at-a-glance monitoring:

1.  **Outbound Connections to Rare Ports:** A table of `winlog.wmicevent_data.DestinationPort` for non-standard ports.
2.  **Processes with Uncommon Parents:** A count of events matching the first KQL query.
3.  **Top  Hashes for  Traffic:** A table of `winlog.event_data.Hashes`.
4.  **Geomap of External Connections:** Plot `destination.geo.location` on a world map to see connections to suspicious countries.

{{<mermaid>}}
flowchart TD
    A[Suspicious Process<br>Sysmon ID 1] --> B{Pivot}
    B --> C[Find all network connections<br>for Process GUID]
    B --> D[Find all child processes<br>of the initial suspect]
    C --> E[Analyze JA3 Hash &<br>Destination IP/Domain]
    D --> F[Review commands executed<br>via LOLBins]
    E --> G[Confirm C2 Infrastructure<br>IoC]
    F --> G
    G --> H[Incident Confirmed]
{{</mermaid>}}

### **Summary: The Mythic Investigation Checklist**

| Phase | What to Look For | Key KQL / Fields |
| :--- | :--- | :--- |
| **Initial Execution** | Unusual parent processes, named payloads | `event.code: 1`, `process.parent.name`, `process.name` |
| **C2 Communication** | Beaconing, non-standard ports, JA3 hashes | `event.code: 3`, `destination.port`, `tls.ja3.hash`, `dns.question.name` |
| **Post-Exploitation** | Discovery commands, credential access, lateral movement | `winlog.event_data.OriginalFileName: ("whoami.exe", "net.exe")`, `winlog.wmicevent_data.DestinationPort: 445` |
| **Pivoting** | Full activity of a process or user | `process.guid`, `winlog.event_data.LogonId` |

**The most important step is pivoting.** A single suspicious event is an indicator; a chain of related events from the same source is evidence of a compromise. By correlating process creation, network activity, and security events, you can uncover even sophisticated C2 frameworks like Mythic.



