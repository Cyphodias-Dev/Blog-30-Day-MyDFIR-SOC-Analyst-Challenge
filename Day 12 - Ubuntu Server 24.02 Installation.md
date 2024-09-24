## Day 12 - Ubuntu Server 24.02 Installation
**Objective:  Setup SSH Server and view authentication logs.**

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

```
2024-09-12T22:30:50.067914+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Failed password for invalid user user7 from 201.124.227.239 port 53304 ssh2
2024-09-12T22:30:51.186779+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Received disconnect from 201.124.227.239 port 53304:11: Bye Bye [preauth]
2024-09-12T22:30:51.186933+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Disconnected from invalid user user7 201.124.227.239 port 53304 [preauth]
2024-09-12T22:31:26.748799+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Invalid user admin from 43.128.107.63 port 38984
2024-09-12T22:31:26.752559+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: pam_unix(sshd:auth): check pass; user unknown
2024-09-12T22:31:26.752641+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=43.128.107.63
2024-09-12T22:31:28.705881+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Failed password for invalid user admin from 43.128.107.63 port 38984 ssh2
2024-09-12T22:31:30.747633+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Received disconnect from 43.128.107.63 port 38984:11: Bye Bye [preauth]
2024-09-12T22:31:30.748676+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Disconnected from invalid user admin 43.128.107.63 port 38984 [preauth]
2024-09-12T22:32:06.641916+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Invalid user user from 103.213.238.91 port 37078
2024-09-12T22:32:06.646099+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: pam_unix(sshd:auth): check pass; user unknown
2024-09-12T22:32:06.646206+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=103.213.238.91
2024-09-12T22:32:09.091320+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Failed password for invalid user user from 103.213.238.91 port 37078 ssh2
2024-09-12T22:32:10.367834+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Received disconnect from 103.213.238.91 port 37078:11: Bye Bye [preauth]
2024-09-12T22:32:10.368133+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Disconnected from invalid user user 103.213.238.91 port 37078 [preauth]
---
```

After I waited a few hours we can see some log entries "failed password" and "Disconnected from invalid user" among others.
Let's try to filter some of this out to only show **failed** entries.

Let's run `grep -i failed auth.log`

```
2024-09-12T22:30:50.067914+00:00 MYDFIR-Linux-Cyphodias sshd[13550]: Failed password for invalid user user7 from 201.124.227.239 port 53304 ssh2
2024-09-12T22:31:28.705881+00:00 MYDFIR-Linux-Cyphodias sshd[13552]: Failed password for invalid user admin from 43.128.107.63 port 38984 ssh2
2024-09-12T22:32:09.091320+00:00 MYDFIR-Linux-Cyphodias sshd[13554]: Failed password for invalid user user from 103.213.238.91 port 37078 ssh2
2024-09-12T22:33:53.714178+00:00 MYDFIR-Linux-Cyphodias sshd[13556]: Failed password for invalid user ts3 from 161.35.25.207 port 47530 ssh2
2024-09-12T22:33:57.348871+00:00 MYDFIR-Linux-Cyphodias sshd[13558]: Failed password for invalid user deployer from 27.254.235.12 port 51930 ssh2
2024-09-12T22:34:22.211401+00:00 MYDFIR-Linux-Cyphodias sshd[13561]: Failed password for invalid user oracle from 91.212.166.37 port 48694 ssh2
2024-09-12T22:35:00.304808+00:00 MYDFIR-Linux-Cyphodias sshd[13563]: Failed password for invalid user admin from 157.230.25.246 port 60514 ssh2
2024-09-12T22:37:04.509277+00:00 MYDFIR-Linux-Cyphodias sshd[13569]: Failed password for invalid user admin from 47.113.224.38 port 53114 ssh2
2024-09-12T22:37:28.166191+00:00 MYDFIR-Linux-Cyphodias sshd[13571]: Failed password for ubuntu from 159.203.110.93 port 58938 ssh2
2024-09-12T22:37:37.768494+00:00 MYDFIR-Linux-Cyphodias sshd[13574]: Failed password for invalid user postgres from 161.35.25.207 port 52540 ssh2
2024-09-12T22:37:38.358219+00:00 MYDFIR-Linux-Cyphodias sshd[13576]: Failed password for invalid user odoo13 from 201.124.227.239 port 49726 ssh2
2024-09-12T22:37:41.916931+00:00 MYDFIR-Linux-Cyphodias sshd[13578]: Failed password for invalid user oracle from 157.230.25.246 port 50694 ssh2
2024-09-12T22:37:52.607145+00:00 MYDFIR-Linux-Cyphodias sshd[13580]: Failed password for invalid user ts3server from 91.212.166.37 port 38828 ssh2
2024-09-12T22:38:16.095744+00:00 MYDFIR-Linux-Cyphodias sshd[13582]: Failed password for invalid user ts3server from 161.35.25.207 port 32828 ssh2
2024-09-12T22:38:20.022168+00:00 MYDFIR-Linux-Cyphodias sshd[13584]: Failed password for invalid user testuser from 157.230.25.246 port 48698 ssh2
...
```

Nice!  Let's go one step further and try filtering just for failed attempts with 'root'.

`grep -i failed auth.log | grep -i root`

```
024-09-12T22:38:31.237243+00:00 MYDFIR-Linux-Cyphodias sshd[13588]: Failed password for root from 91.212.166.37 port 33498 ssh2
2024-09-12T22:42:59.902538+00:00 MYDFIR-Linux-Cyphodias sshd[13693]: Failed password for root from 103.213.238.91 port 36116 ssh2
2024-09-12T22:43:15.644910+00:00 MYDFIR-Linux-Cyphodias sshd[13703]: Failed password for root from 157.230.25.246 port 60078 ssh2
2024-09-12T22:43:49.810078+00:00 MYDFIR-Linux-Cyphodias sshd[13713]: Failed password for root from 161.35.25.207 port 43618 ssh2
2024-09-12T22:43:53.704374+00:00 MYDFIR-Linux-Cyphodias sshd[13715]: Failed password for root from 103.213.238.91 port 49182 ssh2
2024-09-12T22:44:01.943616+00:00 MYDFIR-Linux-Cyphodias sshd[13720]: Failed password for root from 27.254.235.12 port 52954 ssh2
2024-09-12T22:44:10.571615+00:00 MYDFIR-Linux-Cyphodias sshd[13722]: Failed password for root from 91.212.166.37 port 54434 ssh2
2024-09-12T22:45:29.373134+00:00 MYDFIR-Linux-Cyphodias sshd[13752]: Failed password for root from 91.212.166.37 port 47660 ssh2
2024-09-12T22:46:25.498704+00:00 MYDFIR-Linux-Cyphodias sshd[13772]: Failed password for root from 157.230.25.246 port 42044 ssh2
2024-09-12T22:47:38.410183+00:00 MYDFIR-Linux-Cyphodias sshd[13800]: Failed password for root from 161.35.25.207 port 52566 ssh2
2024-09-12T22:47:40.868180+00:00 MYDFIR-Linux-Cyphodias sshd[13802]: Failed password for root from 157.230.25.246 port 59266 ssh2
2024-09-12T22:48:52.409692+00:00 MYDFIR-Linux-Cyphodias sshd[13824]: Failed password for root from 161.35.25.207 port 53912 ssh2
2024-09-12T22:49:25.763273+00:00 MYDFIR-Linux-Cyphodias sshd[13838]: Failed password for root from 91.212.166.37 port 33706 ssh2
2024-09-12T22:49:51.923346+00:00 MYDFIR-Linux-Cyphodias sshd[13850]: Failed password for root from 159.203.110.93 port 38230 ssh2
2024-09-12T22:50:58.545098+00:00 MYDFIR-Linux-Cyphodias sshd[13877]: Failed password for root from 201.124.227.239 port 50086 ssh2
---
```

How about if we only wanted the IPs that were used to gain root access?  We can use the 'cut' command for this. The cut command removes sections of lines to show only what your searching for by selecting a specific ccolumn using a delimiter.  So if we used a space between words as our delimiter our command would look something like the following:

`grep -i failed auth.log | grep -i root | cut -d ' ' -f 9`

The -d ' ' tells the cut command to use a blank or space between each section/word,  The -f '9' will cut out the data at the 9th field/column in each line after the delimiter specified previously.

```
91.212.166.37
103.213.238.91
157.230.25.246
161.35.25.207
103.213.238.91
27.254.235.12
91.212.166.37
91.212.166.37
157.230.25.246
161.35.25.207
157.230.25.246
161.35.25.207
91.212.166.37
159.203.110.93
201.124.227.239
---
```

And here is our list of IP addresses that have attempted a brute force attack to try and gain root access.
