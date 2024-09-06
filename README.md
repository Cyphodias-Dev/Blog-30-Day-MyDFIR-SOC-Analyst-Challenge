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


### Day 7 - Install an Elastic Agent and Fleet Server





























