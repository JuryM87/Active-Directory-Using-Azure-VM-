<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>What is Active Directory?</h2>

Software built and maintained by Microsoft. Centrally manages thousands of user accounts in a single place such as accounts, passwords, permissions etc. It allows you to manage devices and provides a way to control access to resources on a large scale.

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources In Azure
- Ensure Connection between Client and Domain Controller
- Install Active Directory and Admin Creation
- Create X-Amount of Client Users using PowerShell Script

<h2>Setup Resources in Azure</h2>


1. Create the Domain Controller VM (Windows Server 2022) named “DC-1"
   Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
   
   
2. Set Domain Controller’s NIC Private IP address to be static
DC-1 > Networking > NIC > IP Configurations

![vivaldi_zDAEQAVoDh](https://i.imgur.com/CXrdedL.png)


3. Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in the DC-1 step.


4. Ensure that both VMs are in the same Vnet [you can check the topology with Network Watcher]

![vivaldi_z3kENJuYuV](https://i.imgur.com/iJfYltq.png)


![vivaldi_QbUpS9XsXc](https://user-images.githubusercontent.com/109401839/212757249-70c7c150-9627-408f-a285-53b0f9d34a09.png)

<h2>Ensure Connection between Client and Domain Controller<h2>
    
1. Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <IP Address> (perpetual ping)


![vivaldi_QbUpS9XsXc](https://i.imgur.com/UpCX63H.png)
   

Notice that we are getting a "Request timed out." Let's go ahead and fix that.
   
2. Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall, keep client-1 VM open. 

- Start Menu > Windows Defender Firewall with Advanced Secruity program > Inbound Rules > Sort by Porotocol > 

- Enable "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In) Private and Domain Profiles. 2 Inbound Rules.

![Inkedvivaldi_Gb9rFL8rhC](https://i.imgur.com/heh2QTY.png)


3. Check back at Client-1 to see the ping succeed

![vivaldi_WbtokOOBck](https://i.imgur.com/mjYsRxL.png)

Yay!!! Look at the traffic. 
   

<h2>Install Active Directory<h2>


1. Login to DC-1 and install Active Directory Domain Services
   
- Server Manager > "Add Roles and Features" > Check "Active Directory Domain Services"
 

![vivaldi_od5BgUKG6G](https://i.imgur.com/fwJPzlR.png)
   

2. Promote as a DC: Setup a new forest as jury.com (can be anything, just remember what it is)

![2023-01-18 09 37 20 coursecareers com a3928ff24e0f](https://i.imgur.com/fwcvIzY.png)

![2023-01-18 09 38 10 coursecareers com 78e39ae4181d](https://user-images.githubusercontent.com/109401839/213215738-c6379380-e5b8-438b-95a8-6906a16ff339.jpg)

3. Restart and then log back into DC-1 as user: jury.com\labuser
   
4. Search Active Directory User and Computers (ADUC), and creat an Organizational Unit (OU) called "_EMPLOYEES"
   
   ![vivaldi_od5BgUKG6G](https://i.imgur.com/RhF5cJm.png)
   
 5. Create an new OU named "_ADMINS"
   
   ![vivaldi_od5BgUKG6G!](https://i.imgur.com/p3wByA9.png)

6. Create a new employee names "Jane Doe" with the username JaneDoe
   -Add Jane Doe to the "Domain Admins" Security Group
   *Right Click Jane Doe and go to Properites and Follow the illustration below
  
 ![vivaldi_od5BgUKG6G](https://i.imgur.com/a5vADqt.png)
   
 
 ![vivaldi_od5BgUKG6G](https://i.imgur.com/bWyNq08.png)
   
 
 ![vivaldi_od5BgUKG6G](https://i.imgur.com/Sqg2Ewu.png)
   
8. Log out/close the Remote Desktop connection to DC-1 and log back in as “jury.com\janedoe"
   
 ![vivaldi_od5BgUKG6G](https://i.imgur.com/2cHBH8Y.png)
   
9. User Jane Doe as your admin account from now on
   
![vivaldi_od5BgUKG6G](https://i.imgur.com/Rd1xQfL.png)

<h2>Join Client-1 to your domain (jury.com)<h2>

![vivaldi_cRAVrKouac](https://user-images.githubusercontent.com/109401839/213221204-72c7058c-3730-47d9-b9fb-4435ee87c3fd.png)

1. From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
   
![vivaldi_cRAVrKouac](https://i.imgur.com/BhMzhRH.png)
   
2. From the Azure Portal, restart Client-1
   
3. Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
 
   
4. Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain
   
<H2>Setup Remote Desktop for non-administrative users on Client-1<H2>

1. Log into Client-1 as jury.com\janedoe and open system properties

 ![vivaldi_pBr66s3R4C](https://i.imgur.com/OCHs9tc.png)  
   
   ![vivaldi_pBr66s3R4C](https://i.imgur.com/FYNpivC.png)
   
2. Click “Remote Desktop”
   
   
3. Allow “domain users” access to remote desktop


4. You can now log into Client-1 as a normal, non-administrative user now
   
   
5. Normally you’d want to do this with Group Policy that allows you to change MANY systems at once

<H2>Create a bunch of additional users and attempt to log into client-1 with one of the users<H2>

1. Login to DC-1 as janedoe
2. Open PowerShell_ise as an administrator
   
![vivaldi_hbfgkZ3l45](https://i.imgur.com/r1ICGmS.png)
   
3. Create a new File and paste the contents of the [script] below:

[Code Source](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

5. When finished, open ADUC and observe the accounts in the appropriate OU
  
![vivaldi_hbfgkZ3l45](https://i.imgur.com/IUZU2K0.png)
   
6. Attempt to log into Client-1 with one of the accounts (take note of the password in the script)
   
![vivaldi_hbfgkZ3l45](https://i.imgur.com/Zxmz3PX.png)  

![vivaldi_hbfgkZ3l45](https://i.imgur.com/LRZY1A4.png)

Your done. You can do with this many diffent account, already registered but you get the point with this example.
   
I hope you enjoyed this tutorial and I hope to see you in the next one.   
Thank for sticking around until the end. I greatly appreciate it. 
