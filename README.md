# A-Secure-Home-Network-Lab
Setting up a contained, virtual network lab is the essential first step for practicing cybersecurity techniques. This guide focuses on creating a completely isolated environment to ensure that any penetration testing or malware analysis remains separate from your main network.

1. Prerequisites and Tools

2. Lab Architecture and Isolation
The most critical step is network isolation. We will use a Host-Only or Internal network adapter mode within the hypervisor to ensure the virtual machines can talk to each other and your host machine, but cannot access the internet or your home network (Wi-Fi/LAN).
Note: If you need internet access for updates (e.g., updating Kali), you can temporarily switch a VM's adapter to NAT or Bridged and then immediately switch it back to Host-Only once the update is complete.

3. Step-by-Step Setup (Using VirtualBox as Example)
Step 3.1: Install the Hypervisor
Download and install your chosen hypervisor (VirtualBox or VMware Player) on your host operating system (Windows, macOS, or Linux).
Restart your computer after installation if prompted.

Step 3.2: Configure the Isolation Network
This creates the private virtual cable connecting your lab machines
Open VirtualBox and navigate to File > Host Network Manager (or Tools > Network in newer versions).
Click Create to set up a new Host-Only Ethernet Adapter.
Give it an IP range (e.g., set the IPv4 address to 192.168.56.1 and the Netmask to 255.255.255.0).
Crucially: Go to the DHCP Server tab and DISABLE the DHCP Server. This forces you to assign static IP addresses later, which is excellent practice.

Step 3.3: Set up the Target VM (Metasploitable 2)
Metasploitable 2 is often downloaded as a pre-built VM file (an .ova or .vmdk file).
Download the Metasploitable 2 image file.
In VirtualBox, go to File > Import Appliance... and select the downloaded file
Once imported, DO NOT START IT YET. Right-click the VM and go to Settings > Network.
Change the Attached to setting from NAT (the default) to Host-Only Adapter
Select the Host-Only Network you created in Step 3.2.
Start the VM. Log in (default credentials are msfadmin / msfadmin).
Identify its IP: Type ifconfig in the Metasploitable terminal. It will have been assigned an IP in the 192.168.56.x range (e.g., 192.168.56.101). Write this IP down.

Step 3.4: Set up the Attacker VM (Kali Linux)
Download the Kali Linux ISO or the pre-built VM image.
Create a new VM in VirtualBox. Set the OS type to Linux and allocate at least 4 GB of RAM and 4 CPU cores for smooth operation.
Go to Settings > Network for the Kali VM.
Change the Attached to setting to Host-Only Adapter, just like the Target VM.
Start the Kali VM.

Step 3.5: Verify Connectivity
Before you do anything else, make sure your Attacker and Target VMs can communicate across the isolated network.
On the Kali VM terminal, ping the Target VM's IP address (the one you wrote down, e.g., 192.168.56.101).
ping 192.168.56.101
If you receive replies, your lab is successfully isolated and operational.

4. Initial Lab Exercises
Your isolated lab is now ready for controlled practice. Start with these fundamental exercises to build confidence:

Exercise A: Network Discovery
Scan the network: From the Kali terminal, run an Nmap scan against the target
nmap 192.168.56.101
Analyze the results: Identify the open ports and the services running on those ports (e.g., FTP, SSH, HTTP).

Exercise B: Service Exploitation
Launch Metasploit: Still on the Kali terminal, start the Metasploit framework.
msfconsole
Target a common vulnerability: Based on your Nmap scan, find a service you can exploit. For example, Metasploitable 2 has an old, vulnerable version of the VSFTPD service (usually on port 21).
Execute the exploit: Find the appropriate Metasploit module (e.g., use exploit/unix/ftp/vsftpd_234_backdoor), set the target IP, and run it to gain unauthorized access to the target VM.

Exercise C: Defense and Mitigation
Identify the vulnerability: Research the exploit you just used to understand exactly why it worked.
Patch (Simulate): Since Metasploitable is designed to be vulnerable, you can't truly patch it. Instead, document the exact patch or configuration change (e.g., "Upgrade VSFTPD to version 3.0.0 or later") needed to prevent this attack in a real-world scenario.
Alternative Defense: Practice setting up a basic Netfilter or UFW firewall on a separate Linux target VM to block the vulnerable port entirely.
