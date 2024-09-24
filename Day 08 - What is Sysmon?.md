## Day 8 - What is Sysmon?
**Objective:  Learn about Sysmon and what it can do.**

Sysmon is a tool that monitors and logs system activity on an endpoint.  It can monitor actions such as:

- Process Creations
- Network Connections
- File Creations
- And so much more

It can be configured using a configuration file (Which is recommended).

[https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

Exmaples of some comman Event IDs:

**Event ID: 1 - Process Creation**
Sysmon can record the hash of process image files which can be used to perform Open Source Intelligence (OSINT) for further investigation.  The default Hash algorythm is SHA1 but can also use MD5, SHA256, and IMPHASH.  To correlate events it also includes a process Globally Unique Identifier or GUID with process create events.  This is especially helpful when Windows reuses a porcess ID.

**Event ID: 3 - Network Connection**
When it comes to network connections Sysmon can record the following:

- Source IP
- Destination IP
- Ports
- Process

**Note:** This setting is disabled by default.

**File Creations**
**Event ID: 6/7/8 - Driver/Image load & Create Remote Thread**
These are used in detecting defense evasion techniques such as Procsss Injection (a technique where threat actors may inject code into processes).  The downside is they can create a lot of events which can create a lot of false positives.

**Note:**  Event ID: 7 - Image Load is disabled by default.

**Event ID: 10 - Process Access**
This event ID shows up most commonly when attackers are looking to steal credentials by looking into memory for the Local Security Authority or Lsass.exe process.  Pass-the-Hash is an example attack when this event would be present.

**Event ID: 22 - DNSEvent (DNS Query)**
This process is triggered when a DNS is queried.
