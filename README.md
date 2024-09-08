# My Blog for the 30-Day-MyDFIR-SOC-Analyst-Challenge
## 30 days of SOC skills.


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

From the following URL [https://elastic.co/downloads/kibana](https://elastic.co/downloads/kibana) I copied the link after choosing the platform DEB x86_64. and within the same Ubuntu VM we installed Elastcisearch on we downloaded the Kibana deb file using the following command `wget https://artifacts.elastic.co/downloads/kibana/kibana-8.15.0-amd64.deb`.



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


### Day 9














