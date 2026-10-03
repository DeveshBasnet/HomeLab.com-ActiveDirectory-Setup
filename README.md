<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HomeLab Domain Controller Setup</title>
</head>

<body>

<div>
    <h1>HomeLab Active Directory Domain Setup</h1>
    <p>A complete Windows Server Domain Controller (DC) and Active Directory (AD) lab environment built from scratch using virtual machines. Includes DNS, DHCP, Reverse Lookup Zones, User Creation, and Domain Join operations.</p>
</div>

<hr>

<div>
    <h2>Overview</h2>
    <table border="1" cellpadding="6">
        <tr><th>Component</th><th>Value</th></tr>
        <tr><td>Domain Name</td><td>homelab.com</td></tr>
        <tr><td>Domain Controller Hostname</td><td>DC01</td></tr>
        <tr><td>Domain Controller IP</td><td>192.168.4.50</td></tr>
        <tr><td>Network Subnet</td><td>192.168.4.0/24</td></tr>
    </table>
</div>

<hr>

<div>
    <h2>Lab Architecture</h2>
    <pre>
+-----------------------------------------------------------+
|                       HomeLab Network                     |
|                        192.168.4.0/24                     |
+-----------------------------------------------------------+
        |                         |                     |
        |                         |                     |
   +---------+              +-----------+          +-----------+
   |  DC01   |              |  Client01 |          |  Client02 |
   | WinSrv  |              | Win10/11  |          | Win10/11  |
   | AD DS   |              | Domain    |          | Domain    |
   | DNS     |              | Joined    |          | Joined    |
   | DHCP    |              |           |          |           |
   +---------+              +-----------+          +-----------+
        |
        +-- Reverse Lookup Zone
        +-- DHCP Scope
        +-- Test Users
    </pre>
</div>

<hr>

<div>
    <h2>1. Virtual Machine Setup</h2>

    <h3>Windows Server VM (DC01)</h3>
    <ul>
        <li>OS: Windows Server 2022 / 2019</li>
        <li>CPU: 2 cores</li>
        <li>RAM: 4–8 GB</li>
        <li>Disk: 60 GB</li>
        <li>Network:
            <ul>
                <li>Host‑Only / Internal Network</li>
                <li>NAT (optional)</li>
            </ul>
        </li>
    </ul>

    <h3>Client VMs</h3>
    <ul>
        <li>Windows 10 / Windows 11</li>
        <li>2 cores, 4 GB RAM</li>
        <li>Same Host‑Only network as DC01</li>
    </ul>
</div>

<hr>

<div>
    <h2>2. Configure Static IP on DC01</h2>
    <pre>
IP Address:      192.168.4.50
Subnet Mask:     255.255.255.0
DNS Server:      192.168.4.50
    </pre>
</div>

<hr>

<div>
    <h2>3. Install Active Directory Domain Services (AD DS)</h2>
    <ol>
        <li>Open Server Manager</li>
        <li>Add Roles and Features</li>
        <li>Select Active Directory Domain Services</li>
        <li>Install and promote to Domain Controller</li>
        <li>Create new forest: <strong>homelab.com</strong></li>
    </ol>
</div>

<hr>

<div>
    <h2>4. DNS Server Configuration</h2>

    <h3>Forward Lookup Zone</h3>
    <p>Created automatically: <strong>homelab.com</strong></p>

    <h3>Reverse Lookup Zone</h3>
    <pre>
Zone Name: 4.168.192.in-addr.arpa
Network ID: 192.168.4.0
    </pre>

    <h3>PTR Record</h3>
    <pre>
Host IP: 192.168.4.50
Hostname: DC01.homelab.com
    </pre>
</div>

<hr>

<div>
    <h2>5. DHCP Server Setup</h2>
    <pre>
Scope Name: HomeLabDHCP
Start IP:   192.168.4.100
End IP:     192.168.4.150
Subnet:     255.255.255.0
DNS Server: 192.168.4.50
Domain:     homelab.com
    </pre>
</div>
