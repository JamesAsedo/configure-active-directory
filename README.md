# configure-active-directory

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

- Setup Resources in Azure
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain
- Setup Remote Desktop for non-administrative users on Client-1
- Create additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>
<br />

<h3 align="center">Setup Resources in Azure</h3>
<p>
  <h4>Create the Domain Controller VM (Windows Server 2022) named “DC-1”</h4>
  <img src="https://i.imgur.com/MC4M7MC.png" height="50%" width="50%" alt="vm server"/>

  <h4>Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in previous step</h4>
  <img src="https://i.imgur.com/gPRHy9d.png" height="50%" width="50%" alt="vm windows"/>

  <h4>Set Domain Controller’s NIC Private IP address to be static</h4>
  <img src="https://i.imgur.com/Iuq8hk1.png" height="50%" width="50%" alt="static ip"/>

  <h4>Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher)</h4>
  <img src="https://i.imgur.com/D2wHsYH.png" height="50%" width="50%" alt="topology"/>
</p>
<br />

<h3 align="center">Ensure Connectivity between the client and Domain Controller</h3>
<p>
  <h4>Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)</h4>
  <img src="https://i.imgur.com/3LnMqlD.png" height="50%" width="50%" alt="perpetual ping"/>
 
  <h4>Login to the Domain Controller and enable ICMPv4 in on the local windows firewall</h4>
  <img src="https://i.imgur.com/hmw0TKE.png" height="50%" width="50%" alt="enable ICMPv4"/>
  
  <h4>Check back at Client-1 to see the ping succeed</h4>
  <img src="https://i.imgur.com/vqF32a4.png" height="50%" width="50%" alt="ping success"/>
</p>
<br />

<h3 align="center">Install Active Directory</h3>
<p>
  <h4>Login to DC-1 and install Active Directory Domain Services</h4>
  From Server Manager Dashboard, click "Add roles and features"<br/>
    -> click next through the next few windows until you reach the "Select server roles" window<br/>
      -> on the "Select server roles" window, select "AD Domain Services"<br/>
        -> on the pop-up window, click "Add Features" button<br/>
          -> click next through the following windows and then click "install"
  <img src="https://i.imgur.com/uwWKDbR.png" height="50%" width="50%" alt="active directory install"/>
  
  <h4>Promote as a Domain Controller</h4>
  Once intallation is complete, close the window.<br/>
  On the Server Manager Dashboard, look for the flag with the warning symbol and click it<br/>
  A pop up window should open, on that window, click "promote this server to a domain controller"
  <img src="https://i.imgur.com/TlFgMia.png" height="50%" width="50%" alt="domain controller promotion"/>
  
  <h4>Setup a new forest and name the domain (can be anything, just remember what it is, ex: mydomain.com)</h4>
  <img src="https://i.imgur.com/fPZjVyZ.png" height="50%" width="50%" alt="set new forest"/>
  
  <h4>Restart and then log back into DC-1 as user: mydomain.com\labuser</h4>
  <img src="https://i.imgur.com/K8ZTipH.png" height="50%" width="50%" alt="log back in"/>
</p>
<br />

<h3 align="center">Create an Admin and Normal User Account in AD</h3>
<p>
  <h4>In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES” and a second one called "_ADMINS"</h4>
  In the ADUC window, right-click [your domain]<br/>
  -> click "new"<br/>
  -> click Organizational Unit<br/>
  -> create OUs
  <img src="https://i.imgur.com/u424Bu3.png" height="50%" width="50%" alt="create OUs"/>
  <img src="https://i.imgur.com/wp6amjG.png" height="50%" width="50%" alt="OU employees_N_admins"/>
    
  <h4>Create a new admin named “Jane Doe” with the username of “jane_admin”</h4>
  From the ADUC window, under the domain, select "_ADMINS" folder<br/>
  -> righ-click in empty right side of window<br/>
  -> in the pop up window, highlight "new" then select "user"<br/>
  -> create user
  <img src="https://i.imgur.com/RRxzTKh.png" height="50%" width="50%" alt="admin creation"/>
  
  <h4>Add jane_admin to the “Domain Admins” Security Group</h4>
  -> Right-click on user then click properties then click "Member of" then click "Add"<br/>
  -> type "domain" in the "Enter the object names to select" field then click "Check Names"<br/>
  -> select "Domain Admins" in pop up window then click "apply" then click ok
  <img src="https://i.imgur.com/YxLw10E.png" height="50%" width="50%" alt="security group"/>
  
  <h4>Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”. Use jane_admin as your admin account from now on</h4>
  
</p>
<br />

<h3 align="center">Join Client-1 to your domain</h3>
<p>
  <h4>From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address</h4>
  <img src="https://i.imgur.com/xk505ny.png" height="50%" width="50%" alt="client dns settings"/>
  
  <h4>From the Azure Portal, restart Client-1</h4><br/>
  <img src="https://i.imgur.com/itslTXQ.png" height="50%" width="50%" alt="Client-1 restart"/>
  
  <h4>Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)</h4>
  <img src="https://i.imgur.com/gNgez5i.png" height="50%" width="50%" alt="domain joining"/>
  
  <h4>Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain</h4><br/>
  
  <h4>Create a new OU named “_CLIENTS” and drag Client-1 into there</h4>
  <img src="https://i.imgur.com/mzadSgh.png" height="50%" width="50%" alt="active directory client verification"/>
</p>
<br />

<h3 align="center">Setup Remote Desktop for non-administrative users on Client-1</h3>
<p>
  <h4>Log into Client-1 as mydomain.com\jane_admin and open system properties</h4><br/>
  
  <h4>Click “Remote Desktop”</h4><br/>
  
  <h4>Allow “domain users” access to remote desktop</h4><br/>
  
  <h4>You can now log into Client-1 as a normal, non-administrative user now</h4><br/>
  
  <h4>Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)</h4>
  <img src="" height="50%" width="50%" alt="remote desktop setup"/>
</p>
<br />

<h3 align="center">Create a bunch of additional users and attempt to log into client-1 with one of the users</h3>
<p>
  <h4>Login to DC-1 as jane_admin</h4><br/>
  
  <h4>Open PowerShell_ise as an administrator</h4><br/>
  
  <h4>Create a new File and paste the contents of this script (xxx) into it</h4>
  <img src="" height="50%" width="50%" alt="create users script"/>
  
  <h4>Run the script and observe the accounts being created</h4>
  <img src="" height="50%" width="50%" alt="observe create users script"/>

  <h4>When finished, open ADUC and observe the accounts in the appropriate OU and attempt to log into Client-1 with one of the accounts (take note of the password in the script)</h4>
  <img src="" height="50%" width="50%" alt="employee user accounts"/>
  <img src="" height="50%" width="50%" alt="employee user selection"/>
  <img src="" height="50%" width="50%" alt="employee user login"/>
</p>
<br />

<p>
  I hope this tutorial helped you learn a little bit about network security protocols and observe traffic between virtual machines. And although I ran this on a my MacBook Air, this can be easily done on a PC without having to download a remote desktop app since Windows provides that with it's software.
</p>
