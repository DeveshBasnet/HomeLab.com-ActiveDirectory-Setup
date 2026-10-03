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

<img width="1268" height="866" alt="image" src="https://github.com/user-attachments/assets/050a4b6f-eb0b-475f-8031-beff5ab92021" />

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
<br>
    <img width="1296" height="876" alt="image" src="https://github.com/user-attachments/assets/91f244eb-f8b4-445e-a752-5b50968bbdd4" />
<br>
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
    <img width="1207" height="840" alt="image" src="https://github.com/user-attachments/assets/7579bfa0-8581-4668-97b1-b9b8160f90c2" />
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
    <img width="1238" height="873" alt="image" src="https://github.com/user-attachments/assets/166a590c-53e2-4041-be0f-051c55acd706" />
    <pre>
Scope Name: HomeLabDHCP
Start IP:   192.168.4.100
End IP:     192.168.4.150
Subnet:     255.255.255.0
DNS Server: 192.168.4.50
Domain:     homelab.com
    </pre>
</div>


<hr>

<div>
    <h2>Client 01 — TestUser01</h2>
    <p>This section shows the configuration and domain join status for Client01 logged in as TestUser01.</p>
    <h3>Client01 Screenshot</h3>
    <img width="1265" height="902" alt="Domain Join Screenshot"
         src="https://github.com/user-attachments/assets/ae1db2e7-2b64-405f-aa04-d051209e2fe8" />
    <br>
    <img width="1018" height="327" alt="Client01 Screenshot"
         src="https://github.com/user-attachments/assets/a5669ec1-e87d-4270-8165-52b7d07518e7" />
</div>

<hr>

<div>
    <h2>Network Details for TestUser01</h2>
    <p>Network configuration of the client machine after joining the <strong>homelab.com</strong> domain.</p>
    <img width="647" height="443" alt="Network Details"
         src="https://github.com/user-attachments/assets/69df19a3-51bc-42f5-ae97-8cbedfa8a0b4" />
</div>

<hr>

<div>
    <h2>Group Policy Management</h2>
    <p>
        Added the <strong>Workstation - Local Admin Rights</strong> group to the <strong>homelab.com</strong> forest.  
        This allows Domain Admins to automatically become members of the local Administrators group on all domain‑joined PCs.  
        This ensures domain admins can perform administrative tasks on client machines without needing local credentials.
    </p>
    <h3>GPO Screenshot</h3>
    <img width="1268" height="872" alt="image" src="https://github.com/user-attachments/assets/50da2759-1019-47f9-aefb-97fbc091da51" />

</div>

<hr>

<div>
    <h2>Policy Applied on Client PC</h2>
    <p>Below is the confirmation that the Group Policy has been successfully applied on the domain‑joined client machine.</p>
    <h3>Confirmation of IT Level 1 being the admin for the Workstation </h3>
    <img width="1271" height="872" alt="image" src="https://github.com/user-attachments/assets/7e50a87e-0cc2-45b3-baaa-822b97460d77" />

</div>

