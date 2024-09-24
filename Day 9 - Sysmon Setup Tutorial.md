## Day 9 - Sysmon Setup Tutorial
**Objective:  Install Sysmon onto Windows Server and confirm telemetry.**

Today went installed Sysmon by going to the following link:

Sysmon (v15.15 as of this post)
[https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

And we're going to use Olaf Hartong's Sysmon configuration file:

[https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml)

Logging into our Windows Server VM we'll open up a Powershell terminal as Administrator.  Here is the usage info for Sysmon64.exe:
```
System Monitor v15.15 - System activity monitor
By Mark Russinovich and Thomas Garnier
Copyright (C) 2014-2024 Microsoft Corporation
Using libxml2. libxml2 is Copyright (C) 1998-2012 Daniel Veillard. All Rights Reserved.
Sysinternals - www.sysinternals.com

Usage:
Install:                 Sysmon64.exe -i [<configfile>]
Update configuration:    Sysmon64.exe -c [<configfile>]
Install event manifest:  Sysmon64.exe -m
Print schema:            Sysmon64.exe -s
Uninstall:               Sysmon64.exe -u [force]`
  -c   Update configuration of an installed Sysmon driver or dump the
      `current configuration if no other argument is provided. Optionally
       `take a configuration file.
  -i   Install service and driver. Optionally take a configuration file.
  -m   Install the event manifest (done on service install as well)).
  -s   Print configuration schema definition of the specified version.
       `Specify 'all' to dump all schema versions (default is latest)).
  -u   Uninstall service and driver. Adding force causes uninstall to proceed
       `even when some components are not installed.
```
The service logs events immediately and the driver installs as a boot-start driver to capture activity from early in the boot
that the service will write to the event log when it starts.

On Vista and higher, events are stored in "Applications and Services Logs/Microsoft/Windows/Sysmon/Operational". On older
systems, events are written to the System event log.

Use the '-? config' command for configuration file documentation. More examples are available on the Sysinternals website.

`Specify -accepteula to automatically accept the EULA on installation, otherwise you will be interactively prompted to accept it.`

`Neither install nor uninstall requires a reboot.`

I executed the follwing command to install: `.\Sysmon64.exe -i sysmonconfig.xml`

If you see the following, congratulations!  Sysmon is installed.
`The operation completed successfully.`

I verified the Sevice is running.

![Sysmon in Services](https://github.com/user-attachments/assets/e7f46212-0a13-472d-8e5a-3fa5322133cb)

And my first event in the Windows Logs (Even Viewer) under Applications and Services Logs -> Microsoft -> Windows -> Sysmon -> Operationsal

![Sysmon Event Viewer](https://github.com/user-attachments/assets/42173729-3bcf-4eea-8762-219582870bc6)
