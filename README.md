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
   
   Create an new OU named "_ADMINS"
   
   ![vivaldi_od5BgUKG6G!](https://i.imgur.com/p3wByA9.png)

