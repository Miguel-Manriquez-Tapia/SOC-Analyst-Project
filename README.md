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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-19%20002344.png)

### 3. Use SSH via PowerShell
*Note: `<>` in this write-up is a placeholder, do not include in any of your commands.*

1. Start PowerShell with Administrative Privileges.
2. Type: `ssh root@<IP>`
3. Press ENTER.
4. Type "yes" to accept.
5. Paste the password given for your VULTR instance (password will not be visible when pasting). Press ENTER.

### 4. Update Repositories
- Input command: `apt-get update && apt-get update`

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-19%20002350.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-19%20004751.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-19%20002407.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-19%20002415.png)

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
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-27%20012735.png)


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
![imagge alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-28%20131654.png)
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
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-28%20134456.png)
### 7. Go to Vultr
- Add a firewall rule:
  - Accept **TCP** traffic on port **9200**
  - Add the rule

*At this point, you will be able to see CPU and memory details in the Agent's dashboard in Elasticsearch.*
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-28%20134636.png)
### 8. Discover Logs in Elasticsearch
- Navigate to **Discover** in Kibana.
- Search for logs with: `winlog.event_id: 1`
*Now when a log is expanded you will see that Windows Sysmon providing logs to elasticsearch.
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-09-28%20140708.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20121632.png)

3. Change directories:
   cd /var/log
   ls

4. Access authentication logs:
   cat auth.log
   *you can see failed auth attempts with:*
   grep -i failed auth.log | grep -i root

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20145114.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20131947.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20140254.png)

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

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20142118.png)

4. Save the Dashboard:
   - Name: MYDFIR-Authentication-Activity

5. Duplicate the map:
   - Change the name to SSH Successful Authentication
   - Change the settings: go to query and change “Failed” to “Accepted”

![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20142734.png)

6. Save


*Heres a map of 2 servers with a choropleth layer, this shows you how many attempts your servers can and will get from malicious actors. 
![image alt](https://github.com/Miguel-Manriquez-Tapia/SOC-Analyst-Project/blob/main/Screenshot%202024-10-01%20175621.png)
