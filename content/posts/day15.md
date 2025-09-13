+++ 
draft = false
date = 2025-09-11T09:35:25+01:00
title = "Day 15: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 15 "
slug = "SOC Analyst Challenge, Day 15"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++


## Day 15: Remote Desktop Protocol Introduction
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)


**Remote Desktop Protocol (RDP)** is a proprietary protocol developed by Microsoft that provides a user with a graphical interface to connect to another computer over a network connection. The user employs RDP client software for this purpose, while the other computer must run RDP server software.

In simple terms, it allows you to **sit in front of one computer and see and control the desktop of a remote computer** as if you were physically sitting in front of it.

 

### **Key Characteristics & How It Works**

*   **Client-Server Model:** RDP functions on a client-server architecture. The computer being accessed is the *server* (host), and the computer requesting access is the *client*.
*   **Graphical Interface:** It transmits the graphical user interface (GUI) of the remote computer to the client and sends back keyboard and mouse inputs from the client to the server.
*   **Network Efficiency:** RDP is designed to be efficient over network connections. It doesn't constantly stream a video of the remote screen. Instead, it:
    1.  Transmits the initial screen.
    2.  Then only sends *changes* to the screen (e.g., updated windows, mouse movements).
    3.  Compresses and encrypts the data before sending it.
*   **Port:** By default, RDP uses **TCP port 3389**.

{{<mermaid>}}
flowchart TD
    A[RDP Client<br>Your Local Machine] -->|1. Encrypted Session<br>Initiated on Port 3389| B[RDP Server<br>Remote Windows Machine]
    B -->|2. Sends Graphical<br>Display Output| A
    A -->|3. Sends Keyboard<br>& Mouse Input| B
{{</mermaid>}}

 

### **Common Uses of RDP**

1.  **Remote Administration:** System administrators use RDP to manage servers and workstations remotely without needing physical access to the machine. This is its most common enterprise use.
2.  **Remote Work and Support:** Employees can access their office computers from home. IT support staff can also take control of a user's computer to troubleshoot problems remotely.
3.  **Virtual Desktop Infrastructure (VDI):** Technologies like Windows Virtual Desktop use RDP as the underlying protocol to deliver centralized virtual desktops and applications to users.

 

### **Security Risks of RDP**

While incredibly useful, RDP is a frequent target for attackers. Understanding its risks is crucial:

*   **Brute Force Attacks:** This is the most common threat. Attackers use automated tools to try thousands of username/password combinations against RDP servers exposed to the internet until they guess the correct credentials.
*   **Vulnerabilities:** Like any complex software, the RDP service itself has had critical security vulnerabilities in the past (e.g., BlueKeep). If unpatched, these can be exploited for remote code execution.
*   **Credential Theft:** If an RDP session is compromised, attackers can steal login credentials stored on that system.
*   **Lateral Movement:** Once an attacker gains access to one machine via RDP, they can use it as a foothold to attack other systems on the same internal network.
 
 

### **Best Practices for Securing RDP**

**Never expose RDP directly to the public internet without additional security layers.**

1.  **Use a VPN:** The single most effective security measure. Require users to connect to a Virtual Private Network (VPN) first, then use RDP to connect to the internal machine. This hides port 3389 from the entire internet.
2.  **Network Level Authentication (NLA):** **Always enable NLA.** It requires the user to authenticate themselves before a full RDP session is established, which helps protect against denial-of-service and brute-force attacks.
3.  **Strong Password Policies:** Enforce long, complex, and unique passwords for all accounts that have RDP access. Avoid using the built-in Administrator account for RDP.
4.  **Change the Default Port:** Consider changing the default RDP port (3389) to a different number. This doesn't provide real security but reduces noise from automated scanners.
5.  **Firewall Rules:** Restrict RDP access at the firewall level. Only allow connections from specific, trusted IP addresses (e.g., your office's public IP).
6.  **Account Lockout Policies:** Implement account lockout policies that lock an account after a small number of failed login attempts (e.g., 5 attempts), slowing down brute-force attacks.
7.  **Keep Systems Patched:** Ensure both the client and server operating systems are always up-to-date with the latest security patches.
8. **Use [Shodan](https://www.shodan.io/) or [Censys](https://search.censys.io/):** to make sure you have no critical services exposed.

**In summary, RDP is an indispensable tool for remote management and access, but its power also makes it a prime target for attackers. Its use must always be accompanied by strong security measures, with a VPN being the most critical one.**