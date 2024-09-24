## Day 18 - Command and Control Introduction
**Objective:  What is a C2?  Why is it important?  What are some of the tools/frameworks?  What is Mythic and how does it work?**

### Command & Control:  What is it?
"Command and Control consists of techniques that adversaries may use to communicate with systems under their control within a victim network"

[https://attack.mitre.org/tactics/TA0011/](https://attack.mitre.org/tactics/TA0011/)

### Command & Control:  Why is it important?
So attackers can get one step closer to performing their objective such as Credential Access, Lateral Movement, Exfiltration of data, or impact.

### Command & Control:  Comon Tools/Frameworks
There are a ton of C2's out in the wild but here are some of the more common ones:
- **METASPLOIT** from Rapid 7 - Built into Kali Linux and is quite easy to use with many exploits and auxiliaries available [https://www.metasploit.com/](https://www.metasploit.com/)
- **COBALT STRIKE** from Fortra - This is more commerically used and probably the most common.  This makes it easier to detect in the industry.  [https://www.cobaltstrike.com/](https://www.cobaltstrike.com/)
- **SLIVER** from Bishopfox - This framework is open source and convenient to get setup and started with. [https://bishopfox.com/tools/sliver](https://bishopfox.com/tools/sliver)
- **MYTHIC** from Cody Thomas - Built with GoLang, Docker, Docker Compose, and a web browser interface.  You can track your payloads and C@ profiles.
[https://docs.mythic-c2.net/](https://docs.mythic-c2.net/)
