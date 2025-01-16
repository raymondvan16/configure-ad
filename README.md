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

- Step 1: Setting up your DC(Domain Controller) and Client.
- Step 2: Install active directory
- Step 3: Create a Domain Admin user within the domain
- Step 4: Join another "client" to your domain (mydomain.com)
- Step 5: Setup Remote Desktop for non-administrative users on "client"
- step 6: Create a bunch of additional users and attempt to log into "client" with one of the users

<h2>Step 1: Setting up your DC(Domain Controller) and Client..</h2>
Beforehand I created 2 VM's on Azure.
  The first VM I created DC labeled "DC-1" with Windows Server 2022, Region: EAST-US-2, and ip set to static (10.0.0.4).
<img src="https://i.imgur.com/mBawTuI.png" height="80%" width="80%" alt="DC-1 Configuratuon"/>
  The second VM I created was a client VM named "Client-1" with windows 10, Region: EAST-US-2, and DNS server set to the same as DC-1 ip address (10.0.0.4).
<img src="https://i.imgur.com/CKFw1hf.png" height="80%" width="80%" alt="Client-1 Configuration"/>
  
<h2>Step 2: Install active directory.</h2>
Login to DC-1 and install Active Directory Domain Services.
You can do this buy clicking  "add roles and features" shown here. 
(Leave everything else as default except for "server roles" as you install AD as shown.)
<img src="https://i.imgur.com/aqTRD1b.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
Afterwards you can promote the server as a DC.
<img src="https://i.imgur.com/vVS7GzR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
Then setup your forest. Choose any name you want just make sure to remember it. Afterwards we will restart server and log back to the VM with the new forest followed by the user. (ex. mydomain.com\labuser) is mine.
<img src="https://i.imgur.com/9lAc6Qy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<h2>Step 3: Create a Domain Admin user within the domain.</h2>
-Once you log back to the same VM using the new forest, in the windows search bar search up "Active Directory Users and Computers" and launch it.
-Under the forest selection, right click-> new-> Organizational unit, create an OU named "_EMPLOYEES".
-Repeat again and this time create "_ADMINS"
<img src="https://i.imgur.com/r7bANHh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
Now under "_EMPLOYEES" right click-> new -> Users and create a new users. In this lab:
Jane Doe
"jane_Admin"/ Cyberlab123! (username/password)
Uncheck user must change password on next logon.
<img src="https://i.imgur.com/7atfoze.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
Now under "_EMPLOYEES" right click Jane Doe -> Properties -> Member of -> Add... -> type "Domain Admins" and add Jane to Domain Admins.
Log out / close the connection to DC-1 and log back in as “mydomain.com\jane_admin”.
User jane_admin as your admin account from now on.

<h2>Step 4: Join another "client" to your domain (mydomain.com)</h2>
-Log into the client VM under original account. 
-right click start menu -> system -> "Rename this PC" (advanced) -> Computer name tag click "change" -> Member of- Domain: "mydomain.com"
<img src="https://i.imgur.com/9H8y0MN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
-When prompted login, log into "mydomain.com\jane_admin" and select restart.
-Once restarted, it will be a member of the domain.
-Log into DC, verify that Client is in ADUC by
  search "Active Directory Users and Computers" -> expand "mydomain.com" -> "computers" -> verify client is in.

<h2>Step 5: Setup Remote Desktop for non-administrative users on "client"</h2>
-Log into Client-1 as mydomain.com\jane_admin
-Open system properties -> Click “Remote Desktop” -> Select users that can remotely access this PC. -> Allow “domain users” access to remote desktop
<img src="https://i.imgur.com/PDZbpIp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
-You can now log into Client-1 as a normal, non-administrative user now

<h2>step 6: Create a bunch of additional users and attempt to log into "client" with one of the users"</h2>
-Login to DC-1 as jane_admin
-Open PowerShell_ise as an administrator
-Create a new File and paste the contents of the script down below into it
https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
-Run the script and observe the accounts being created
<img src="https://i.imgur.com/X47nVAX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
-When finished, open ADUC and observe the accounts in the appropriate OU　(_EMPLOYEES)
-Attempt to log into Client-1 with one of the accounts (take note of the password in the script)

End of Lab.
