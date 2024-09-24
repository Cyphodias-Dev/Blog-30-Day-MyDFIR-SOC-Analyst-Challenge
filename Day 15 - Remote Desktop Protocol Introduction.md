## Day 15 - Remote Desktop Protocol Introduction
**Objective:  Learn about RDP, why it is used, how attackers abuse it, how to find endpoints with exposed RDP and how you can pretect yourself.**

**RDP (Remote Desktop Protocal** - "used for communication between the Terminal Server and the Terminal Server Client. RDP is encapsulated and encrypted within TCP."

Original KB number:   186607

[https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol)

Default Port:  3389 with TCP

### Why it's used
Unlike SSH RDP allows you to connect via a Graphical User Interface (GUI) as if you were using your own Windows environment locally.  It saves time, easily accessible, and conveinient.

### How does it get abused?
Attackers can perform scans on IP ranges for this port to find if RDP's port is open or not and then attempt a brute force attack to gain access for malicious activites such as data exfiltraion, ransomeware or both.

### How to find exposed RDP Server
****DISCLAIMER** - For the following sites **DO NOT** try to brute force any of these unless you have explicit consent from the owner.  This is for educational purposes only.

**Shodan** - [https://www.shodan.io/](https://www.shodan.io/)

****Note** - This site requires a valid login for many of it's features.

Shodan allows us to search for `port: 3389` in the search field at the top and will show us just how many open RDP ports there are by country.

![Shodan port 3389 search results](https://github.com/user-attachments/assets/0764ddd5-8346-4e7e-9ddb-21492e2ae486)

**Censys** - [https://search.censys.io/](https://search.censys.io/)

Going straight to the search page we can just type in `3389` in the search field and view the amount of open ports open here as well.

![censys search results for port 3389](https://github.com/user-attachments/assets/fb851b18-4387-422d-9f30-82361fa49cbf)

### How to protect yourself
1. **Turn RDP Off** - This can be used in dev environments and once a project reaches the production stage RDP can sometimes never get disabled.  If it's not used or needed it's better to turn it off.
2. **Use Multifactor Authentication** - MFA should be used whenever availabled to proved you with an additional layer of protection.  MFA is not guaranteed to protect you 100% but is a good deterrent and better than not having it enabled at all.
3. **Restrict Access** - A firewall or VPN is a good way to prevent unauthorized access.  These options can prevent the majority of brute force attacks from attackers.
4. **Better Passwords** - A good password policy should always be implemented in a company environment or personal use.  15+ characters with uppercase, lowercase, numbers, and special characters.  For even more security a Priviledged Access Managemennt tool (PAM) which provides you with a one time password.
5. **Change Default Accounts** - Whenever possible change these accounts to something other than the norm.  As well as not using the same logins for all your accounts  Think about it this way, if attackers already know your username and/or password because it was a left as default, that's one step closer for attackers to gain access to your accounts/logins.
