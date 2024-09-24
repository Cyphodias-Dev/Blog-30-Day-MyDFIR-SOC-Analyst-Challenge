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
