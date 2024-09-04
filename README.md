# My Blog for the 30-Day-MyDFIR-SOC-Analyst-Challenge
30 days of SOC skills.


# Day 1 - Introduction & Diagram

Today we had a look at what our environment will look like for this project and how everything will work together by creating the following diagram.

![2024-09-03_21-28-29](https://github.com/user-attachments/assets/e6830c50-2249-4b45-8410-655c7c8d58d6)


# Day 2 - ELK Stack

This is the main part of our project used to collect, filter and view our data with the following three tools.  Elasticsearch, Logstash, and Kibana.

E - Elasticsearch - Our Indexer / Search Head. The haart of the ELK Stack.  This is a database at its core able to store your logs from multiple sources.  This uses a query language called ES|QL (Elasticsearch Query Language).  You can also access data from other apps via Restful APIs or JSON.
L - Logstash - The Heavy Forwarder.  This is the processing pipeline that can ingest your data/telemetry from multiple sources to filter and output it to your Elasticsearch instance.  Two of the most common ways to collect data are using Beats and Elastic Agents (more on this later).
K - Kibana - Web GUI.  This is essentially our dashboard which we'll be using to sort through and view our logs.

With these 3 items we can:
- Search Data
- Create Visulizations
- Create Reports
- Create Alerts

