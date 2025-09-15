+++ 
draft = false
date = 2025-09-13T18:15:27+01:00
title = "Day 24: osTicket Setup Tutorial on Windows Server 2022"
description = "SOC Analyst Challenge, Day 24"
slug = "SOC Analyst Challenge, Day 24"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++


## Day 24: osTicket Setup Tutorial on Windows Server 2022
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)
  

### **Prerequisite: Install XAMPP & osTicket**

1.  Install **XAMPP** to `C:\xampp`.
2.  Download **osTicket**, extract it, and rename the `upload` folder to `osticket` in `C:\xampp\htdocs\`.

 

### **Part 1: Install XAMPP**

1.  **Download XAMPP** from [https://www.apachefriends.org/index.html](https://www.apachefriends.org/index.html) (PHP 8.x version).
2.  **Run the Installer.** Install to `C:\xampp`. Select **Apache, MySQL, PHP, and phpMyAdmin**.
3.  **Start the Services.** Open the **XAMPP Control Panel** and start **Apache** and **MySQL**.

 

### **Part 2: Modify MySQL root and pma Accounts for Network Access**

This step is optional for a lab but good practice. It secures your database.

1.  Go to `http://localhost/phpmyadmin/`.
2.  Click on the **User accounts** tab.
3.  Find the **root** user with the **Server** listed as `localhost`. Click **Edit privileges**.
4.  Click the **Login Information** tab.
5.  **Change the password:** Enter a strong new password. **Remember this password.**
6.  **Change the host:** To allow connections from your network, change `localhost` to `ip@windows-server` (which means "any host"). **Warning: This is less secure for production.** Find your server's IP address using `ipconfig` in Command Prompt.

7.  Click **Go**. Repeat these steps for the `pma` (phpMyAdmin) user if it exists.

 

### **Part 3: Create the Database**

1.  In **phpMyAdmin**, click the **Databases** tab.
2.  Under **Create database**, enter the name `osticket`.
3.  Set the Collation to `utf8_general_ci`.
4.  Click **Create**.

 

### **Part 4: Install osTicket Files**

1.  **Download osTicket** from [https://osticket.com/download/](https://osticket.com/download/).
2.  **Extract** the ZIP file.
3.  Copy the **`upload`** folder to `C:\xampp\htdocs\`.
4.  Rename the folder from `upload` to `osticket`.

 

### **Part 5: Set File Permissions**

1.  **Open Command Prompt as Administrator.**
2.  Navigate to the osTicket directory:
    ```cmd
    cd C:\xampp\htdocs\osticket
    ```
3.  **Rename the config file:**
    ```cmd
    icacis include\ost-config php /reset
    ```
	* 	What this does: The /reset flag replaces all existing permissions on the file with the default inherited permissions. This securely locks the configuration file, preventing it from being easily modified by the web server, which is the correct final state.

### **Part 6: Run the osTicket Web Installer**

1.  From a browser on your network, go to: `http://<YOUR-SERVER-IP>/osticket/`.
2.  **Complete the installation form:**
    *   **Database Settings:**
        *   **MySQL Database:** `osticket`
        *   **MySQL Username:** `root`
        *   **MySQL Password:** *The new password you set in Part 2.*
        *   **MySQL Host:** `ip@windows-server`
    *   Fill out the **Site Settings** and admin user details.
    *   Click **Install Now!**.



Your osTicket installation is now complete and more secure. The key fixes were using the correct `icacls` command and remembering the new database password during the web setup.