<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this lab, I demonstrate how I observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create our Resources and VMs
- Observe ICMP Traffic
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic

<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/acnEmOa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create our Resources and VMs:
  
Create a resource group > Create a Windows 10 Virtual Machine > select the previously made resource group while creating the vm > allow it to create a new Virtual Network (Vnet) and Subnet
</p>
<br />

<p>
<img src="https://i.imgur.com/V6Trzv5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a Linux (Ubuntu) VM > while creating the vm, select the same resource group that was used when creating the Windows 10 VM > under the "Networking" tab, make sure to select the Vnet that was previously created > Create 
</p>
<br />

<p>
<img src="https://i.imgur.com/z2x2B3z.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Observe ICMP Traffic:
  
Remote Desktop into VM1 with it's private IP address and the credentials that were made when creating VM1
</p>
<br />

<p>
<img src="https://i.imgur.com/Ugd5i9q.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In VM1, open the web browser and go to https://www.wireshark.org/download.html to download and install Wireshark with the defaults. 
</p>
<br />

<p>
<img src="https://i.imgur.com/roJloXm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Open Wireshark and observe the traffic.
  
Wireshark > click the blue fin icon on the upper left to start capturing packets (this is going to allow ne to observe the live traffic that is happening on the vm).
</p>
<br />

<p>
<img src="https://i.imgur.com/a4utwFA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Filter for ICMP (internet control message protocol) traffic.
  
In the search bar tyoe "icmp" and hit enter. Now all traffic is being filtered for ICMP. ICMP is the protocol that "ping" uses, which is a command tool used to check if another computer is reachable on the network line.
</p>
<br />

<p>
<img src="https://i.imgur.com/lBBaJBg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Ping VM2 in VM1
  
Retrieve VM2's private IP address in Azure > open Windows Powershell in VM1 > in Powershell type "ping 10.0.0.5" (VM2's private IP address).
  
Now you can observe the ICMP traffic that was created in Wireshark.
</p>
<br />

<p>
<img src="https://i.imgur.com/N1OjJcS.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a perpetual ping form VM1 to VM2
  
Clear the data in Wireshark by clicking the green fin icon on the upper left > Continue without saving > in Powershell type "ping 10.0.0.5 -t"
  
Now the perpetual ICMP traffic can be observed.
</p>
<br />

<p>
<img src="https://i.imgur.com/GzDJnlL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
While the perpetual ping is happening, I am going to change the firewall on VM2 to not allow ICMP traffic to come through.
  
In Azure, search "Network security groups" > VM2-nsg > Inbound security groups > Add > choose Destination port ranges: ICMP > Action: Deny > Priority: 200 > name the rule: DENY_ICMP_PING_FROM_ANYWHERE > Add
</p>
<br />

<p>
<img src="https://i.imgur.com/XqqTrjE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now you can see that the perpetual ping stopped working and we stopped receiving echo replies from VM2 in Wireshark because ICMP traffic is being blocked by the network security group, so there is no chance for VM2 to receive it and send a reply.
  
Now that I have demonstrated this, I went back and deleted the inbound rule so that VM2 can continue receiving ICMP traffic and I cancelled the perpetual ping.
</p>
<br />

<p>
<img src="https://i.imgur.com/r5eYXFX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Observe SSH Traffic:
  
SSH essentially allows access to the other computers command line.
  
Now, instead of ICMP, I am going to filter by SSH traffic in Wireshark.
  
Instead of pinging into VM2, I am going to SSH into it from VM1. 
</p>
<br />

<p>
<img src="https://i.imgur.com/lxExLBa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now that the SSH connection has been established, I can use Linux commands in Powershell in VM1 to control VM2. Whenever something is typed it goes over the nework and the SSH traffic can be seen coming through Wireshark.
  
Now that I have demonstrated that, I typed the command "exit" to close the connection and go back to VM1's command line, and ctrl-c to close the SSH connection.
</p>
<br />

<p>
<img src="https://i.imgur.com/xMH1eWh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Observe DHCP Traffic: 
  
DHCP is used to automatically assign your IP address. Filter for DHCP traffic in Wireshark.
  
I can force the renewall of an IP address with the command "ipconfig /renew". I should be able to see DHCP traffic coming through Wireshark.
</p>
<br />

<p>
<img src="https://i.imgur.com/FsNwV8y.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Observe DNS Traffic: 
  
DNS (domain name system) converts human readable domain names (such as www.google.com) to IP addresses, which computers can use and understand.
  
Filter for DNS traffic in Wireshark.
  
To observe DNS traffic, I entered the command "nslookup www.google.com" in Powershell. Observe how it returned Google's IP address.
</p>
<br />

<p>
<img src="https://i.imgur.com/H1q9E29.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Observe RDP Traffic:
  
RDP (Remote Desktop Protocol) is used when remotely connecting from one computer to another to gain a remote desktop GUI. The computer being connected to is typically "listening" for a connection on TCP port 3389.
  
Whenever I do anything on my local computer like move my mouse or type something, it sends traffic to VM1 which RDP is listening for. This can be observed in Wireshark in VM1.
  
That is all for this lab where I observed Network Security Groups (NSGs) and inspected traffic between Azure Virtual Machines.
