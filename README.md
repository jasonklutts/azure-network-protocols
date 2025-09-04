# Network Security Groups (NSGs) and Inspecting Network Protocols Using Wireshark

In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups.

---

## Video Demonstration
[Network Security Groups and Inspecting Network Protocols with Wireshark](https://www.youtube.com/watch?v=w8H5fWBHddA&t=24s)

---

## Environments and Technologies Used
- Microsoft Azure Windows and Linux Virtual Machines  
- Remote Desktop Connection  
- PowerShell and Various Command-Line Tools  
- Network Protocols: **SSH, RDP, DNS, HTTP/S, ICMP**  
- Wireshark (packet analyzer / network sniffer)

---

## Operating Systems Used
- Windows 10 Pro  
- Linux Ubuntu  

---

## High-Level Steps
1. Observe **ICMP Traffic** (Internet Control Message Protocol)  
2. Configure a Firewall (**Network Security Group**)  
3. Observe **SSH Traffic** (Secure Shell)  
4. Observe **DHCP Traffic** (Dynamic Host Configuration Protocol)  
5. Observe **DNS Traffic** (Domain Name System)  
6. Observe **RDP Traffic** (Remote Desktop Protocol)  

---

## Actions and Observations

### Step 1: Observing ICMP Traffic
Make sure both Virtual Machines are running.  
Start by logging into the **Windows Virtual Machine** using Remote Desktop Connection.  
Copy and paste the public IP address of the Windows VM and supply credentials to log in.  

![Windows VM Login](Screenshot-2025-06-27-122056.png)  
![Windows VM RDC](Screenshot-2025-06-27-122135.png)  

In Wireshark, type: icmp


This filters only ICMP traffic.  

Open **PowerShell** and run: ping 10.0.0.5


This command pings the **Linux VM private IP address**. Wireshark will capture and display ICMP request/reply packets, confirming successful connectivity between the VMs.

![ICMP Filter](scrnshot-4.png)  
![Ping Command](scrnshot-2.png)  
![ICMP Traffic Captured](scrnshot-5.png)  

---

### Step 2: Configuring a Firewall (Network Security Group)
Run a perpetual ping from the Windows VM to the Linux VM: ping 10.0.0.5 -t


![Ping -t Command](scrnshot-ping-t.png)  

In Azure, open the **Network Security Group (NSG)** for the Linux VM.  
- Disable incoming ICMPv4 traffic  
- Add a new inbound rule with source/destination as `*` (ICMP does not use ports)  
- Save and apply  

![NSG Settings](Screenshot-2025-06-27-123217.png)  
![NSG Inbound Rule](Screenshot-2025-06-27-123636.png)  

After enabling the new inbound security rule, Wireshark now shows: Request timed out


This confirms the NSG is blocking ICMP packets.  

---

### Step 3: Observing SSH Traffic
In Wireshark, filter for **SSH traffic**: ssh


From PowerShell on the Windows VM, connect via SSH: ssh labuser@10.0.0.5


- Accept the fingerprint  
- Enter Linux VM credentials  
- Connection is established successfully  

![SSH Command](ssh-command.png)  
![SSH Filter](ssh-filter-after-connection.png)  

SSH uses **TCP Port 22**  

---

### Step 4: Observing DHCP Traffic
In Wireshark, filter for **DHCP**: dhcp


From PowerShell (as Administrator) on the Windows VM: ipconfig /renew


This forces the system to request a new IP address. DHCP traffic will be visible in Wireshark.  

![DHCP Renew](ipconfig-renew.png)  
![DHCP Traffic Filtered](dhcp-filtered.png)  

DHCP uses **UDP Ports 67 & 68**  

---

### Step 5: Observing DNS Traffic
In Wireshark, filter for **DNS**: dns


From PowerShell, run:
nslookup google.com
nslookup disney.com


This displays resolved IP addresses for each domain, and DNS traffic is visible in Wireshark.  

![nslookup Google](nslookup-google.png)  
![nslookup Disney](nslookup-disney.png)  

DNS uses **TCP/UDP Port 53**  

---

### Step 6: Observing RDP Traffic
In Wireshark, filter for **RDP**: rdp


RDP is used for graphical remote connections between computers.  

![RDP Filtered](rdp-filtered.png)  

RDP uses **TCP Port 3389**  

---

















