
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>HomeLab Active Directory Domain Setup</title>
  <style>
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      margin: 0;
      padding: 2rem;
      background: #0f172a;
      color: #e5e7eb;
      line-height: 1.6;
    }
    h1, h2, h3 {
      color: #facc15;
      margin-top: 1.5rem;
    }
    h1 {
      font-size: 2rem;
    }
    h2 {
      font-size: 1.5rem;
    }
    h3 {
      font-size: 1.2rem;
    }
    a {
      color: #38bdf8;
      text-decoration: none;
    }
    a:hover {
      text-decoration: underline;
    }
    code {
      background: #020617;
      padding: 0.15rem 0.35rem;
      border-radius: 4px;
      font-family: "Consolas", "Fira Code", monospace;
      font-size: 0.9rem;
    }
    pre {
      background: #020617;
      padding: 1rem;
      border-radius: 8px;
      overflow-x: auto;
      font-size: 0.9rem;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin: 1rem 0;
      font-size: 0.95rem;
    }
    th, td {
      border: 1px solid #1f2937;
      padding: 0.5rem 0.75rem;
      text-align: left;
    }
    th {
      background: #111827;
      color: #e5e7eb;
    }
    tr:nth-child(even) {
      background: #020617;
    }
    .tagline {
      color: #9ca3af;
      margin-bottom: 1rem;
    }
    .section {
      margin-bottom: 2rem;
    }
    .badge-row {
      margin-bottom: 1rem;
    }
    .badge {
      display: inline-block;
      background: #111827;
      color: #e5e7eb;
      padding: 0.25rem 0.6rem;
      border-radius: 999px;
      font-size: 0.75rem;
      margin-right: 0.4rem;
      border: 1px solid #1f2937;
    }
    ul {
      margin-left: 1.2rem;
    }
    li {
      margin: 0.25rem 0;
    }
    hr {
      border: none;
      border-top: 1px solid #1f2937;
      margin: 2rem 0;
    }
  </style>
</head>
<body>

  <h1>🏠 HomeLab Active Directory Domain Setup</h1>
  <p class="tagline">
    A complete Windows Server Domain Controller (DC) and Active Directory (AD) lab environment built from scratch using virtual machines.
    Includes DNS, DHCP, Reverse Lookup Zones, User Creation, and Domain Join operations.
  </p>

  <div class="badge-row">
    <span class="badge">Windows Server</span>
    <span class="badge">Active Directory</span>
    <span class="badge">DNS</span>
    <span class="badge">DHCP</span>
    <span class="badge">Homelab</span>
  </div>

  <hr>

  <div class="section">
    <h2>📌 Overview</h2>
    <p>
          <img width="1037" height="800" alt="image" src="https://github.com/user-attachments/assets/d1817a78-a270-423d-9305-6f48dc6a3111" />

      This document describes the full setup of a homelab Active Directory domain running on a Windows Server virtual machine.
      It covers VM creation, AD DS installation, DNS and DHCP configuration, user creation, and joining client machines to the domain.
    </p>

    <h3>Domain Information</h3>
    <table>
      <tr>
        <th>Component</th>
        <th>Value</th>
      </tr>
      <tr>
        <td>Domain Name</td>
        <td><code>homelab.com</code></td>
      </tr>
      <tr>
        <td>Domain Controller Hostname</td>
        <td><code>DC01</code></td>
      </tr>
      <tr>
        <td>Domain Controller IP</td>
        <td><code>192.168.4.50</code></td>
      </tr>
      <tr>
        <td>Network Subnet</td>
        <td><code>192.168.4.0/24</code></td>
      </tr>
    </table>
  </div>

  <div class="section">
    <h2>🧱 Lab Architecture</h2>
    <pre><code>+-----------------------------------------------------------+
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
</code></pre>
  </div>

  <div class="section">
    <h2>🖥️ 1. Virtual Machine Setup</h2>

    <h3>Windows Server VM (DC01)</h3>
    <ul>
      <li><strong>OS:</strong> Windows Server 2022 / 2019</li>
      <li><strong>CPU:</strong> 2 cores</li>
      <li><strong>RAM:</strong> 4–8 GB</li>
      <li><strong>Disk:</strong> 60 GB</li>
      <li><strong>Network Adapters:</strong>
        <ul>
          <li>Adapter 1: Host-Only / Internal Network</li>
          <li>Adapter 2 (optional): NAT (for updates)</li>
        </ul>
      </li>
    </ul>

    <h3>Client VMs</h3>
    <ul>
      <li><strong>OS:</strong> Windows 10 / Windows 11</li>
      <li><strong>CPU:</strong> 2 cores</li>
      <li><strong>RAM:</strong> 4 GB</li>
      <li><strong>Network:</strong> Same Host-Only network as DC01</li>
    </ul>
  </div>

  <div class="section">
    <h2>🌐 2. Configure Static IP on DC01</h2>
    <p>Configure the network adapter on the Domain Controller with a static IP:</p>
    <pre><code>IP Address:      192.168.4.50
Subnet Mask:     255.255.255.0
Default Gateway: (optional)
DNS Server:      192.168.4.50
</code></pre>
  </div>

  <div class="section">
    <h2>🏛️ 3. Install Active Directory Domain Services (AD DS)</h2>

    <h3>Using Server Manager</h3>
    <ol>
      <li>Open <strong>Server Manager</strong>.</li>
      <li>Click <strong>Add Roles and Features</strong>.</li>
      <li>Select <strong>Active Directory Domain Services</strong> and install.</li>
      <li>After installation, promote the server to a Domain Controller.</li>
      <li>Create a new forest with the domain:
        <pre><code>homelab.com</code></pre>
      </li>
      <li>Allow the server to reboot after promotion.</li>
    </ol>
  </div>

  <div class="section">
    <h2>📡 4. DNS Server Configuration</h2>

    <h3>Forward Lookup Zone</h3>
    <p>A forward lookup zone for <code>homelab.com</code> is created automatically during AD DS installation.</p>

    <h3>Reverse Lookup Zone</h3>
    <p>Create a reverse lookup zone for the 192.168.4.0/24 network:</p>
    <pre><code>Zone Name: 4.168.192.in-addr.arpa
Network ID: 192.168.4.0
</code></pre>

    <h3>PTR Record</h3>
    <p>Add a pointer record for the Domain Controller:</p>
    <pre><code>Host IP:   192.168.4.50
Hostname:  DC01.homelab.com
</code></pre>
  </div>

  <div class="section">
    <h2>📶 5. DHCP Server Setup</h2>
    <p>Install the DHCP Server role via Server Manager and configure a scope.</p>

    <h3>Create DHCP Scope</h3>
    <pre><code>Scope Name: HomeLabDHCP
Start IP:   192.168.4.100
End IP:     192.168.4.150
Subnet:     255.255.255.0
Router:     (optional)
DNS Server: 192.168.4.50
Domain:     homelab.com
</code></pre>

    <p>Authorize the DHCP server in Active Directory after configuration.</p>
  </div>

  <div class="section">
    <h2>👤 6. Active Directory User Creation</h2>

    <h3>Example Users</h3>
    <ul>
      <li>John Doe</li>
      <li>Jane Smith</li>
      <li>TestUser1</li>
      <li>TestUser2</li>
    </ul>

    <h3>PowerShell Example</h3>
    <pre><code>New-ADUser -Name "TestUser1" -SamAccountName testuser1 `
-AccountPassword (ConvertTo-SecureString "Password123" -AsPlainText -Force) `
-Enabled $true
</code></pre>
  </div>

  <div class="section">
    <h2>💻 7. Domain Join Client Machines</h2>

    <h3>Client Configuration</h3>
    <ol>
      <li>Set the client DNS server to <code>192.168.4.50</code>.</li>
      <li>On Windows 10/11, go to:
        <pre><code>Settings → System → About → Domain Join</code></pre>
      </li>
      <li>Enter the domain name:
        <pre><code>homelab.com</code></pre>
      </li>
      <li>Provide domain admin credentials when prompted.</li>
      <li>Reboot the client machine.</li>
    </ol>

    <h3>Verify Domain Join</h3>
    <p>On DC01, open <strong>Active Directory Users and Computers</strong> and check the <strong>Computers</strong> container. Joined clients should appear under the domain.</p>
  </div>

  <div class="section">
    <h2>🛠️ 8. Managing Domain Joined Machines</h2>

    <h3>Group Policy Management</h3>
    <p>Common Group Policy tasks include:</p>
    <ul>
      <li>Password policies</li>
      <li>Login banners</li>
      <li>Desktop wallpaper enforcement</li>
      <li>Software restriction policies</li>
      <li>Drive mappings</li>
    </ul>

    <h3>Remote Management Tools</h3>
    <ul>
      <li><code>gpupdate /force</code></li>
      <li><code>rsop.msc</code></li>
      <li><code>eventvwr</code></li>
      <li>PowerShell Remoting</li>
    </ul>
  </div>

  <div class="section">
    <h2>📁 Suggested Repository Structure</h2>
    <pre><code>/docs
  ├── VM-Setup.md
  ├── AD-Setup.md
  ├── DNS-Config.md
  ├── DHCP-Config.md
  ├── Domain-Join.md
  └── Users-GPO.md

README.md
</code></pre>
  </div>

  <div class="section">
    <h2>✅ Completed Tasks</h2>
    <ul>
      <li>Domain Controller installed</li>
      <li>Active Directory Domain Services configured</li>
      <li>DNS forward and reverse lookup zones created</li>
      <li>PTR record for DC configured</li>
      <li>DHCP scope configured</li>
      <li>Test users created</li>
      <li>Client VMs joined to <code>homelab.com</code></li>
      <li>Basic domain management tested</li>
    </ul>
  </div>

  <div class="section">
    <h2>📬 Next Steps (Optional Enhancements)</h2>
    <ul>
      <li>Add a File Server with NTFS permissions</li>
      <li>Deploy WSUS for Windows updates</li>
      <li>Add Kali Linux for AD security testing</li>
      <li>Deploy Wazuh or Splunk for SIEM</li>
      <li>Apply a hardened Group Policy baseline</li>
    </ul>
  </div>

</body>
</html>
      





Forward Lookup Zones

<img width="1285" height="887" alt="image" src="https://github.com/user-attachments/assets/e8518039-5cb1-46da-a65d-3c0af43083a6" />


Reverse Lookup Zones

<img width="1082" height="811" alt="image" src="https://github.com/user-attachments/assets/62b650ff-cad7-4e71-8a32-0878e580a3a7" />


