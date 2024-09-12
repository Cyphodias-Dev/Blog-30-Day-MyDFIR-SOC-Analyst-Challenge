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



### Day 1 - Introduction & Diagram

Today I got a look at what the environment will look like for this project and how everything will work together by creating the following logical diagram.

![30Day-MyDFIR-Diagram](https://github.com/user-attachments/assets/e6830c50-2249-4b45-8410-655c7c8d58d6)



### Day 2 - ELK Stack

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



### Day 3 - Create your own Elasticsearch instance 

I created my Elasticsearch instance in the cloud with MyDFIR's $300 credit through VULTR cloud services.  I was able to setup a Virtual Private Cloud (VPC) that will contain all of the virtual machines.  Next I set up a VM running Ubuntu 22.04 LTS (80GB NVMe storage, 4 vcores, & 16 GB memory) and was able to SSH into it from my host PC.  I copied the link from Elasticsearch's download page (platform DEB x86_64) [https://elastic.co/downloads/elasticsearch](https://elastic.co/downloads/elasticsearch) and installed it on my Ubuntu instance.  Once completed, I configured a firewall all within the initial VPC.  The only real modifications that needed to be done was to update the Ubuntu distro and modify the Elasticsearch YAML file for the proper IP address and port so the SOC Analyst instance will be able to connect to it.



### Day 4 - Kibana Setup

From the following URL [https://elastic.co/downloads/kibana](https://elastic.co/downloads/kibana) I copied the link after choosing the platform DEB x86_64. and within the same Ubuntu VM we installed Elastcisearch where we downloaded the Kibana deb file using the following command `wget https://artifacts.elastic.co/downloads/kibana/kibana-8.15.0-amd64.deb`.



### Day 5 - Windows Server 2022 Installation

While installing the Windows Server 2022 VM Steven made a slight modification to the setup and added the changes to our inital logical diagram.  We're going to leave the Windows server and Ubuntu Server outside the VPC.  This will help protect our Fleet and Ticket Servers from being attacked if there was a potential breach.  This will also provide Windows Event Logs that we can use later on.

![30Day-MyDFIR-Diagram-2.0](https://github.com/user-attachments/assets/0f9e44ec-f12f-4312-9c1b-446080f0c8c5)




### Day 6 - Elastic Agent and Fleet Server Introduction

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


### Day 7 - Elastic Agent and Fleet Server Setup Tutorial

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


### Day 8 - What is Sysmon?

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


### Day 9 - Sysmon Setup Tutorial
Today went installed Sysmon by going to the following link:

Sysmon (v15.15 as of this post)
[https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

And we're going to use Olaf Hartong's Sysmon configuration file:

[https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml)

Logging into our Windows Server VM we'll open up a Powershell terminal as Administrator.  Here is the usage info for Sysmon64.exe:

`System Monitor v15.15 - System activity monitor`
`By Mark Russinovich and Thomas Garnier`
`Copyright (C) 2014-2024 Microsoft Corporation`
`Using libxml2. libxml2 is Copyright (C) 1998-2012 Daniel Veillard. All Rights Reserved.`
`Sysinternals - www.sysinternals.com`

`Usage:`
`Install:                 Sysmon64.exe -i [<configfile>]`
`Update configuration:    Sysmon64.exe -c [<configfile>]`
`Install event manifest:  Sysmon64.exe -m`
`Print schema:            Sysmon64.exe -s`
`Uninstall:               Sysmon64.exe -u [force]`
  `-c   Update configuration of an installed Sysmon driver or dump the`
      `current configuration if no other argument is provided. Optionally`
       `take a configuration file.`
  `-i   Install service and driver. Optionally take a configuration file.`
  `-m   Install the event manifest (done on service install as well)).`
  `-s   Print configuration schema definition of the specified version.`
       `Specify 'all' to dump all schema versions (default is latest)).`
  `-u   Uninstall service and driver. Adding force causes uninstall to proceed`
       `even when some components are not installed.`

`The service logs events immediately and the driver installs as a boot-start driver to capture activity from early in the boot
that the service will write to the event log when it starts.`

`On Vista and higher, events are stored in "Applications and Services Logs/Microsoft/Windows/Sysmon/Operational". On older
systems, events are written to the System event log.`

`Use the '-? config' command for configuration file documentation. More examples are available on the Sysinternals website.`

`Specify -accepteula to automatically accept the EULA on installation, otherwise you will be interactively prompted to accept it.`

`Neither install nor uninstall requires a reboot.`

I executed the follwing command to install: `.\Sysmon64.exe -i sysmonconfig.xml`

If you see the following, congratulations!  Sysmon is installed.
`The operation completed successfully.`

I verified the Sevice is running.

![Sysmon in Services](https://github.com/user-attachments/assets/e7f46212-0a13-472d-8e5a-3fa5322133cb)

And my first event in the Windows Logs (Even Viewer) under Applications and Services Logs -> Microsoft -> Windows -> Sysmon -> Operationsal

![Sysmon Event Viewer](https://github.com/user-attachments/assets/42173729-3bcf-4eea-8762-219582870bc6)


### Day 10 - Elasticsearch Ingest Data Tutorial

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


### Day 11 - What is a Brute Force Attack?
 
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


### Day 12 - Ubuntu Server 24.02 Installation
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



