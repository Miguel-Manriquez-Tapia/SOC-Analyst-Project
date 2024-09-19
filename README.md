<h1>SOC Analyst Project</h1>

 ### [YouTube Demonstration](https://youtu.be/)

<h2>Description</h2>
ELK Stack...
<br />


<h2>Languages and Utilities Used</h2>

- <b>Elasticsearch</b> 
- <b>Kibana</b>
- <b>Powershell</b>

<h2>Environments Used </h2>

- <b>VULTR VPC 2.0</b> 
- <b>Ubuntu LTS 22.04</b> 
- <b>Windows Server 2022</b>

<h2>Network Diagram:</h2>
<img src="https://i.imgur.com/Oc04Y2R.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>



<h2>Project walk-through:</h2>

*Note: For the process of signing up to services, please follow the service website's directive.*  
<br />
The cloud provider we will use for this project is VULTR.<br/>
Let begin:

**1. Create a Virtural Private Cloud 2.0 network on VULR.**<br/> 
     Quick Explanation: A VPC is a networking service that offers improved security, scalability, and performance, allowing users to create isolated, customizable cloud environments with advanced networking features.<br/>
     <br />
    -Set the network location.<br />
    -Configure the IPv4 range. *You can decide to do this manually or let VULTR set it automatically. <br />
    -Name the Network.<br />
    -Deploying a new server. Location should be the same as VPC. <br />
    -For this project we will be using:
    -Ubuntu 22.04 LTS X64<br />
     -80GB GB NVMe4 vCPUs<br />
     -Memory: 16Gbs<br />
      Bandwidth: 6 TBs<br />
      Select: VPC 2.0<br />
      *Establish your own IP<br />

    
1.  1\. Choose your operating system, for this guide we will be using:  
    Ubuntu 22.04 LTS X64  
    80GB GB NVMe4 vCPUs  
    Memory: 16Gbs  
    Bandwidth: 6 TBs
    
    Select: VPC 2.0  
    \* Establish your own IP
    

  

  

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_898b209a.png)  

2.  After setting you host name and server label (should be the same). Deploy your server instance.
    
    Click the view console option in VULTR to see if the instance is ready to connect via SSH.
    

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_ef0ce6e2.png)  

  

Use the protocol SSH via PowerShell.  
\*<> in this write up is a placeholder, do not include in any of you commands from here and forward.

To SSH on into your VPC instance:

1.  Start PowerShell with Administrative Privileges.
    
2.  Type: ssh root@<IP>
    
3.  ENTER
    
4.  yes (accept)
    

5.Paste in the password given for your VULTR instance. (after pasting it is not visible, that is how command prompts work), ENTER

5.a. Update Repositories

\-input command: apt-get update && apt-get update-

  

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_de86c317.png)  

3\. Download Elasticsearch.

\-go to [https://www.elastic.co/downloads/elasticsearch](https://www.elastic.co/downloads/elasticsearch)

\-select: deb x86\_64 and copy the address link

4\. Go back to PowerShell

\-input command: wget <paste elastic search address link>

5\. Install Elasticsearch

\-input command: dpkg -i elasticsearch (tab to auto complete)

6.Open your notepad and save the “security auto configuration info”

\-The superuser info is saved in this file.

7\. :Change Directories. Use: cd /etc/elasticsearch/

\-check the contents of the directory: ls -a

8.Note: elasticsearch.yml

\-contains the contents of the configuration settings for the instance

9.Modify elasticsearch.yml file

\-input: nano elasticsearch.yml

\-remove the #(comment) for network.host: <local host>

\-paste your public ip

\-remove the # for port

\-close nano - \*ctrl x, save (yes), enter

10\. Implement Security: Firewall

\- Go to VULTR

\-Network tab

\-Firewall tab

\-Add firewall group

\-Change source to: My IP, add rule.

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_5577a002.png)  

11.Start up Elasticsearch Service

\-input: systemctl

daemon-reload

enable elasticsearch.service

start elasticsearch.service

\-confirm elasticsearch is running.

\-systemctl status elasticsearch.service

  

**KIBANA SETUP**

  

1.Visit elastic.co/downloads//kibana

  

2\. Choose platform, for this project we are using:

\-DEB x86\_64

\-Copy the path (copy link address)

3\. Create a new PowerShell window, with administrative privileges.

\-input: ssh root@<ip>

\*you are going to ssh into your VULTR instance.

4.Look at directory contents with: input: ls -a

\-notice: .deb file

5.Installing kibana

\-input: dpkg -i kibana - 8.15.1 -amd64.deb

6\. We need to modify the configuration file.

\-open nano

\-input: nano /etc/kibana/kibana.yml

\-remove the # (comment) from the server port and server host

\*important: for the server host enter the VULTR IP of you instance. Leave the port 5601

7.Update the repositories for Kibana

\-systemctl

daemon-reload

enable kibana.service

start kibana.service

status kibana.service

\*kibana should have the status of: active

  

8.Generate an elasticesearch enrollment token for kibana

\-Change directories: input: cd /usr/share/elasticsearch/bin

\-Generate token: input: ./elasticsearch-create-enrollment-token –scope kibana

\-Save token generated in notepad.

\-Troubleshooting: if you you get “Connection timeout”, it most likely the firewall rules.

9\. Go to VULTR.

\-Network tab

\-Firewall

\-Accept TCP , Range: 1-65535, Source: My IP

  

10.Configure Firewall rule on Ubuntu VM

\-input: cd / (to navigate to home directory)

\-input: ufw allow 5601 (this command allows port 5601 to communicate through the firewall)

11\. Change directories

\-input: cd /usr/share/kibana/bin

you will need to execute the binary file with: input: ./kibana-verification-code(tab for auto complete)

\*a user code will be generated

12.Create a user, password with the file elasticsearch superuser file

13.Generate encryption key

\-input: ./kibana-encryption-keys generate

IMPORTANT: 3 keys are genrated

\-1st = Objects key

\-2nd = Reporting key

3rd = Security key

\*Save to notepad

  

14\. Store keys in binary file “keystore”

\-input: ./kibana-keystone add “pastenameofkey” (1,2,3 from step 13)

15\. Repeat for 1-3 keys.

16\. Input: systemctl restart kibana.service (TAB for autocomplete)

  

  

**Deploying, creating, and installing a Windows Server (2022)**

  

1\. Deploy the new instance.

\-Setting for this project:

Cloud compute – shared CPU

Location – Same as other instances

Image: Windows Standard 2022 x64

Regular: Cloud Compute

Storage 55 GB SSD

Memory: 2 GB

Cores: 1 vCPU

Bandwidth: 2TB

  

2\. Create host name and server name.

3\. Click on console to determine when the VM is fully installed and running.

4\. Start VM

Click: show keyboard

show extra keys

password is located in VULTR

paste the password in clipboard

Windows Server is now running.

\*Let check if you can connect remotely:

1st Copy ip of the Windows Server

2nd Open RDP in a VM or local computer

3rd Paste IP into “Computer” and it will ask for password, connect.

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_b2a26c99.png)  

  

  

  

**Elastic agent & Fleet Server Setup**

  

1.Deploy new instance on VULTR. (Settings for this project are as follows)

\-Optimized cloud compute dedicated CPU

\-Location: Same as before

\-Image Ubuntu 22.04 LTS x64

\-30GB NVMe

\-Core: 1vCPU

\-Memory:4GB

\-Storage: 30GB NVMe

\-Bandwidth: 4GB

2.Log in to your public ip via port number 5601

elastic search

3.Fleet Server uses: 8220

URL = https//ip

4.Go to PowerShell

ssh into fleetserver

\-input: ssh [root@ip](mailto:root@ip)

\*Note:add firewall rule to ELK stack VM to allow fleet to acess

\-input: curl cmd from “install fleet server to centralized host”

add “—fleet server-port = 8220”

\-troubleshoot if needed, check your steps and commands additional modification may be needed.

5\. Connect to ELK VM via SSH:

  

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_145342d9.png)
  

![](file:///C:/Users/migue/AppData/Local/Temp/lu5414440namq.tmp/lu5414440nape_tmp_145342d9.png)
