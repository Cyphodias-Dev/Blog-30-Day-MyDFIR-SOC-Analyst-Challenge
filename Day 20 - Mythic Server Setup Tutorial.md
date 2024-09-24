## Day 20 - Mythic Server Setup Tutorial
**Objective:  Setup Mythic C2 Server and learn how Mythic works.**

In VULTR (or your cloud provider of choice) we're going to spin up an Ubuntu 22.04 VM for our Mythic C2 Server.  The minimum you specs you want to use is 2 CPUs and 4 GB of RAM.  We also chose Cloud Compute-Shared CPU without Auto Backups or IPv6.  And for the Server Hostname we called it `MyDFIR-MYTHIC`.

While that's getting ready you can go to Kali.org and download the Kali Linux Hypervisor vm of your choice.  If you don't already have it you will also need to download a copy of 7-Zip from 7-zip.org.  Once you have 7-Zip installed and the VM file is downloaded you can execute the .vmx file which should open in the you default VM.

Back to our Mythic VM in the cloud we can SSH into it with you CLI of choise with the credentials listed on your cloud provider.  And, just like before, we'll updated it with the following:
```apt update && apt upgrade -y```

Now we can start installing our prerequisites for Mythic.
- **Docker Compose** - `apt install docker-compose`
- **Make** - Should already be install.  At the time of this blog the latest version is 4.3-4.1build1.
- **Mythic** - `git clone https://github.com/its-a-feature/Mythic`

Once Mythic is cloned we'll move into the Mythic directory `cd Mythic`.  Next we want to install the Ubuntu version of Docker with the following commmand: `install_docker_ubuntu.sh `.  Let's restor our docker service by entering: `systemctl restart docker` and then we'll verify it's running by entering `systemctl status docker`.  Hopefully, you should get somethijng like the below listing.

```
root@MyDFIR-MYTHIC:~/Mythic# systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2024-09-22 02:56:48 UTC; 2s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 29662 (dockerd)
      Tasks: 9
     Memory: 20.4M
        CPU: 451ms
     CGroup: /system.slice/docker.service
             └─29662 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

The next thing we'll run is the command `make`. This should finish right away.  And finally we can start the Mythic CLI with: `./mythic-cli start`

For our Mythic C2 Server let's also add a firewall on it to only allow our IP through and avoid tons of scan attampes from bots and attackers.  We can add our host IP on all ports for SSH along with the RDP Windows Server and the SSH Linux Server fir TCP on all ports.

Now we should be able to log into our Mythc dsshboard with your Mythic Server IP from your cloud VM using https and port 7443.  The username will be `mythic_admin` and you can find your password under the hidden file .evn in the Mythic directory.  You can run `cat .env` and it will be listed under "MYTHIC_ADMIN_PASSWORD".

There are lots of options in here that we'll use in the coming days.  Feel free to explore the options to get comfortable with what each section does.

![Mythic Dashboard](https://github.com/user-attachments/assets/1769b4bd-30e4-4cae-ad7a-ed61083d0a70)
