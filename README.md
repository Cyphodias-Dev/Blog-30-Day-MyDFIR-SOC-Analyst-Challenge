# My Blog for the 30-Day-MyDFIR-SOC-Analyst-Challenge
30 days of SOC skills.



## Day 1 - Introduction & Diagram

Today I got a look at what our environment will look like for this project and how everything will work together by creating the following diagram.

![2024-09-03_21-28-29](https://github.com/user-attachments/assets/e6830c50-2249-4b45-8410-655c7c8d58d6)



## Day 2 - ELK Stack

This is the main part of our project used to collect, filter and view our data with the following three tools.  Elasticsearch, Logstash, and Kibana.

- E - Elasticsearch - Our Indexer / Search Head. The haart of the ELK Stack.  This is a database at its core able to store your logs from multiple sources.  This uses a query language called ES|QL   
      (Elasticsearch Query Language).  You can also access data from other apps via Restful APIs or JSON.
- L - Logstash - The Heavy Forwarder.  This is the processing pipeline that can ingest your data/telemetry from multiple sources to filter and output it to your Elasticsearch instance.  Two of the most common 
      ways to collect data are using Beats and Elastic Agents (more on this later).
- K - Kibana - Web GUI.  This is essentially our dashboard which we'll be using to sort through and view our logs.

With these 3 items we can:
- Search Data
- Create Visulizations
- Create Reports
- Create Alerts

You can find out more about the from the following URL:
[https://www.elastic.co/elastic-stack/features](https://www.elastic.co/elastic-stack/features)



## Day 3 - Create your own Elasticsearch instance 

I created my Elasticsearch instance in the cloud with MyDFIR's $300 credit through VULTR cloud services.  I was able to setup a Virtual Private Cloud that will contain all of our virtual machines.  Next I set up a VM running Ubuntu 22.04 LTS (80GB NVMe storage, 4 vcores, & 16 GB memory) and was able to SSH into it from my host PC.  I copied the link from Elasticsearch's download page (platform DEB x86_64) [https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.15.0-amd64.deb](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.15.0-amd64.deb) and installed it on my Ubuntu instance.  Once completed, I configured a firewall all within the initial VPC.  The only real modifications that needed to be done was to update the Ubuntu distro and modify the Elasticsearch YAML file for the proper ip address and port so the SOC Analyst instance will be able to connect to it.



## Day 4 - Kibana Setup

From the following URL [https://elastic.co/downloads/kibana](https://elastic.co/downloads/kibana) I copied the link after choosing the platform DEB x86_64. and within the same Ubuntu VM we installed Elastcisearch on we downloaded the Kibana deb file using the following command `wget https://artifacts.elastic.co/downloads/kibana/kibana-8.15.0-amd64.deb`.


## Day 5 - Windows Server 2022 Installation


## Day 6 - Elastic Agent and Fleet Server Introduction
