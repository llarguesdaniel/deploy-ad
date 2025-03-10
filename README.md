
# deploy-ad
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- <a href="#Prepare_Active_Directory_Infrastucture">Prepare Active Directory Infrastucture</a> 

- <a href="#Deploy_Active_Directory">Deploy Active Directory</a>

- <a href="#Create_Users_with_Powershell">Create Users with Powershell</a>
  
- <a href="#Group_Policy_and_Manage_Accounts">Group Policy and Manage Accounts</a>



---


<h2>Deployment and Configuration Steps</h2>
<h3><a id="Prepare_Active_Directory_Infrastucture">Part 1: Prepare Active Directory Infrastucture</a></h3> 

1. ***Setup Domain Controller***
  - Navigate to the Azure Portal and create a new Resource Group for the lab environment.

![rgname](https://github.com/user-attachments/assets/6745f8cd-bfcd-4c6d-a685-04594e6369ee)


2. ***Create a Virtual Network and Subnet***
 - Choose previously made Resource Group
 - Set up Virtual Network and Subnet
![vnname](https://github.com/user-attachments/assets/59e3ce40-0ddf-45b2-bb5a-b9583a237dab)



3. ***Create the Domain Controller VM (Windows Server 2022)***
  - Name Virtual Machine `DC-1`
  - Verify VM is on the Virtual Network created
![vmnamesize](https://github.com/user-attachments/assets/c5f0f4bd-c14f-4705-a858-ba6e7daa7ec6)

![vmuserandagree](https://github.com/user-attachments/assets/cb28b196-dc77-4192-88ca-ba465bc4d4e9)

 4. ***Set Static Private IP for DC-1***
  - Once VM is deployed, go to `DC-1` Network Interface Card (NIC)
  - Change Private IP from Dynamic to Static 
![dc1networksettings2](https://github.com/user-attachments/assets/5d78d5f7-3399-4566-ad63-4eed922e7f06)

![nicstaticip](https://github.com/user-attachments/assets/5b9aa34f-8191-4918-b505-e11c2e9da39e)

5. ***Disable Windows Firewall***
  - Log in to `DC-1`
  - Navigate to `Windows Firewall`
  - Disable all Firewall settings
![dcfirewallsettings](https://github.com/user-attachments/assets/cda79919-1528-4665-b90f-66eeb462e567)


1. ***Create the Client VM (Windows 10)***
  - Name the VM: `Client-1`
![clinetnamesize](https://github.com/user-attachments/assets/dd83a4a3-10ad-46bd-810c-8ce23d2ccbf2)

![clientpassword](https://github.com/user-attachments/assets/0738d722-f00a-434d-b194-c212b21f0f75)

 2. ***Attach Client-1 to the Same Region and Virtual Network*** 
  - Ensure `Client-1` is in the same Virtual Network and Subnet as `DC-1`
![clientsubnet](https://github.com/user-attachments/assets/68d15794-2660-45c6-81db-eaae4aed0b89)


3. ***Set Client-1’s DNS Settings***
  - Change `Client-1` DNS Settings to match `DC-1` Private IP Address
![clientnic](https://github.com/user-attachments/assets/9cf2746d-92e7-41a8-9a19-b941c472fa49)

![clientdns](https://github.com/user-attachments/assets/c447eade-701c-48a0-8c29-c75b564111f3)

4. ***Test Connectivity***
  - Login to `Client 1`
  - Open `Powershell`
  - Ping `DC-1`
![clientping](https://github.com/user-attachments/assets/f312367e-096f-4307-beb2-b3d9b4fd7b6b)


5. ***Verify DNS Settings***
  - Run `ipconfig /all` to verify DNS 
![clinetipconfig](https://github.com/user-attachments/assets/ff45df57-6bd6-401a-ac83-df5406efc892)


<br />


---

<h3><a id="Deploy_Active_Directory">Part 2: Deploy Active Directory</a></h3>

Install Active Directory
1Log in to DC-1
2install Active Directory Domain Services
3Promote `DC-1` as a Domain Controller and setup a new forest (mydomain.com)
4Restart DC-1 and log in as user: mydomain.com\labuser

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Create a Domain Admin user
1Navigate to Active Directory Users and Computers (ADUC)
2create an Organizational Unit (OU) called “_EMPLOYEES” and  named “_ADMINS”
3Create a new employee named “Jane Doe”
namejane doe
username “jane_admin”
password Cyberlab123!
4Add jane_admin to the “Domain Admins” Security Group
5Log out of DC-1 and log back in as “mydomain.com\jane_admin”

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Join Client-1 to your domain
1Login to Client-1 as the original local admin (labuser) and join it to the domain (computer will restart)
2Login to the Domain Controller and verify Client-1 shows up in ADUC
3Create a new OU named “_CLIENTS” and drag Client-1 into there

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<br />


---

<h3><a id="Create_Users_with_Powershell">Part 3: Create Users with Powershell</a></h3>

Setup Remote Desktop for non-administrative users 
1Log into Client-1 as mydomain.com\jane_admin
2Open system properties
Click “Remote Desktop”
Allow “domain users” access to remote desktop

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Create a bunch of additional users and attempt to log into client-1 with one of the users
Login to DC-1 as jane_admin
Open PowerShell_ise as an administrator
attempt to log into Client-1 with one of the accounts (take note of the password in the script)

<br />

---

<h3><a id="Group_Policy_and_Manage_Accounts">1.) Group Policy and Manage Accounts</a></h3>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

