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
