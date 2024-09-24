## Day 11 - What is a Brute Force Attack?
**Objective:  Learn more about brute force attacks, common tools and how to protect yourself.**
 
- **Brute Force Attack** - The attempt to try every combination of a password to gain access.  A good example of this is MyDFIR's luggage combination where every combination is attempted.

- **Dictionary Attack** - This attack will normally use a collection of either words, phrases, or passwords to gain access to a person's login.  These collections of passwords are normally found from credential or password dumps obtain from a past data breach from a website or company.  A commonly known list is the rockyou.txt password list.

- **Credential stuffing** - The act in trying multiple username and password combinations in the hopes of gaining access.

**Protection from brute force attacks**

- Use longer passwords or long password phrases. 
- 15+ characters is recommended with a combination of upper and lowercase letters, numbers, and special characters (!@#$%).
- Use a password manager.
- Try using a psss phrase (eg. Please subscribe to MyDFIR)
- Use multifactor authentication (MFA or 2FA).  Can be in the form of Text, Email, Authenticator app (app is recommended)
- Stay vigilente is a different mindset to question all types of emails or texts received for potential phishing or smishing attacks

**Example brute force attack tools**
These common tools can all be found by default in Kali Linux.
- Hydra [https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)
- Hashcat [https://hashcat.net/hashcat/](https://hashcat.net/hashcat/)
- John the Ripper [https://www.openwall.com/john/](https://www.openwall.com/john/)

You can test brute force attacks out with MyDFIR's following lab if you like:
[Active Directory Project (Home Lab)](https://www.youtube.com/watch?v=5OessbOgyEo&list=PLG6KGSNK4PuBWmX9NykU0wnWamjxdKhDJ&index=13)
