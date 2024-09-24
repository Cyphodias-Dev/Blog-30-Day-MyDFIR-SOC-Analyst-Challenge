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
