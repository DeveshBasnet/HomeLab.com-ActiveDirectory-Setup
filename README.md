# HomeLab.com-ActiveDirectory-Setup

This repository documents the full setup of a homelab Active Directory domain running on a Windows Server VM.

Domain Information
Component	Value
Domain Name	homelab.com
Domain Controller Hostname	DC01
Domain Controller IP	192.168.4.50
Network Subnet	192.168.4.0/24

      

<img width="1037" height="800" alt="image" src="https://github.com/user-attachments/assets/d1817a78-a270-423d-9305-6f48dc6a3111" />


🌐 2. Configure Static IP on DC01
Set the DC’s network adapter:

IP Address:      192.168.4.50
Subnet Mask:     255.255.255.0
Default Gateway: (optional)
DNS Server:      192.168.4.50

Forward Lookup Zones

<img width="1285" height="887" alt="image" src="https://github.com/user-attachments/assets/e8518039-5cb1-46da-a65d-3c0af43083a6" />


Reverse Lookup Zones

<img width="1082" height="811" alt="image" src="https://github.com/user-attachments/assets/62b650ff-cad7-4e71-8a32-0878e580a3a7" />


🏛️ 3. Install Active Directory Domain Services (AD DS)
Using Server Manager
Add Roles and Features

Select Active Directory Domain Services

Install

Promote server to Domain Controller

Create new forest:

Code
homelab.com
DC01 will reboot after promotion.

📡 4. DNS Server Configuration
Forward Lookup Zone
Created automatically during AD DS installation:

Code
homelab.com
Reverse Lookup Zone
Create manually:

Code
Zone Name: 4.168.192.in-addr.arpa
Network ID: 192.168.4.0
PTR Record
Add pointer for the DC:

Code
Host IP: 192.168.4.50
Hostname: DC01.homelab.com
📶 5. DHCP Server Setup
Install DHCP role via Server Manager.

Create DHCP Scope
Code
Scope Name: HomeLabDHCP
Start IP:   192.168.4.100
End IP:     192.168.4.150
Subnet:     255.255.255.0
Router:     (optional)
DNS Server: 192.168.4.50
Domain:     homelab.com
Authorize DHCP server in AD.
