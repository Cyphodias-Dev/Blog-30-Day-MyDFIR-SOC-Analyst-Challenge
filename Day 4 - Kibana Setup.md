## Day 4 - Kibana Setup
**Objective:  Learn how to install your own Kibana instance.**

From the following URL [https://elastic.co/downloads/kibana](https://elastic.co/downloads/kibana) I copied the link after choosing the platform DEB x86_64. and within the same Ubuntu VM we installed Elastcisearch where we downloaded the Kibana deb file using the following command `wget https://artifacts.elastic.co/downloads/kibana/kibana-8.15.0-amd64.deb`.



## Day 5 - Windows Server 2022 Installation
**Objective:  Have a Windows Server with RDP exposed to the internet.**

While installing the Windows Server 2022 VM Steven made a slight modification to the setup and added the changes to our inital logical diagram.  We're going to leave the Windows server and Ubuntu Server outside the VPC.  This will help protect our Fleet and Ticket Servers from being attacked if there was a potential breach.  This will also provide Windows Event Logs that we can use later on.

![30Day-MyDFIR-Diagram-2.0](https://github.com/user-attachments/assets/0f9e44ec-f12f-4312-9c1b-446080f0c8c5)
