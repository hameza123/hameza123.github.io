+++ 
draft = false
date = 2025-09-10T20:09:45+01:00
title = "Sysmon Setup Tutorial, Day 9 "
description = "SOC Analyst Challenge, Day 9 "
slug = "SOC Analyst Challenge, Day 9 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC","Sysmon"]
series = []
+++


## Day 9: Sysmon Setup Tutorial
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

 

### **Sysmon Setup Tutorial**


### **Part 1: Download Sysmon**

1.  Go to the official Microsoft Sysinternals page: [**Download Sysmon**](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
2.  Download the ZIP file and extract it to a folder on your Windows machine .

 

### **Part 2: Get a Security-Focused Configuration File**

Running Sysmon with its default config generates too much noise. We use a config file to tell it what to log and filter out benign activity.

**Recommended Config: SwiftOnSecurity's sysmon-config**
This is a highly regarded, community-tested configuration.

1.  Go to the GitHub page: [**sysmon-config**](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)
2.  Download the raw XML file:
    *   Click on the file named `sysmonconfig-export.xml`.
    *   Click the **Raw** button to view the raw XML file.
    *   Right-click on the page and select **Save As...**.
    *   Save the file to your Sysmon folder.

 

### **Part 3: Install Sysmon with the Configuration**

We will install Sysmon from the command line with the configuration.

1.  Open **Command Prompt or PowerShell as an Administrator**.
2.  Navigate to your Sysmon directory:
    ```cmd
    cd C:\Download\Sysmon
    ```
3.  Run the installation command using the downloaded config file:
    ```cmd
    sysmon.exe -accepteula -i sysmonconfig-export.xml
    ```
    *   `-accepteula`: Automatically accepts the End User License Agreement.
    *   `-i`: Stands for "install" and loads the configuration file.

4.  You should see output similar to this:
    ```
    System Monitor v14.0 - System activity monitor
    Copyright (C) 2014-2022 Mark Russinovich and Thomas Garnier
    Sysinternals - www.sysinternals.com

    Loading configuration file with schema version 4.82
    Configuration file validated.
    Sysmon installed.
    SysmonDrv installed.
    Starting SysmonDrv.
    Sysmon started.
    ```
    This confirms Sysmon is installed and running as a service with your custom configuration.



### **Part 4: Verify the Installation and View Logs**

1.  Open **Event Viewer** (Press `Win + R`, type `eventvwr.msc`, and press Enter).
2.  Navigate to: **Applications and Services Logs** > **Microsoft** > **Windows** > **Sysmon** > **Operational**.
    

3.  You should now see a stream of detailed events. Double-click on any event to see the rich details it provides (Process GUID, Hashes, Parent Process, etc.).



### **Useful Sysmon Commands**

*   **Update Configuration:** If you change your config file, reload it without reinstalling:
    ```cmd
    sysmon.exe -c sysmonconfig-export.xml
    ```
*   **Uninstall Sysmon:**
    ```cmd
    sysmon.exe -u
    ```
*   **Check Current Configuration:**
    ```cmd
    sysmon.exe -c
    ```

### **Troubleshooting**

*   **No Events in Log?** Ensure the `Sysmon` service is running. (Open `services.msc` and find "Sysmon" or "System Monitor").
*   **Configuration Error:** If the XML schema version is wrong, download the latest config file that matches your Sysmon version.
*   **Logs are Too Noisy/Quiet:** Edit the `sysmonconfig-export.xml` file to fine-tune what is logged. Comment out or modify rules to fit your environment.

You have now successfully installed and configured Sysmon, turning your Windows system into a powerful source of detailed security telemetry.