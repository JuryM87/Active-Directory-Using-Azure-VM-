<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

Welcome back! This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

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

![vivaldi_zDAEQAVoDh](https://i.imgur.com/NfN9iyq.png)

3. Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in the DC-1 step.
4. Ensure that both VMs are in the same Vnet [you can check the topology with Network Watcher]
Here is an illustration of what we are doing: 

![vivaldi_z3kENJuYuV](https://i.imgur.com/dC1yrGE.png)


![vivaldi_QbUpS9XsXc](https://user-images.githubusercontent.com/109401839/212757249-70c7c150-9627-408f-a285-53b0f9d34a09.png)

<h2>Ensure Connection between Client and Domain Controller<h2>

1. Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)

![vivaldi_3DGaaVQRmB](https://user-images.githubusercontent.com/109401839/213212386-519dc0bd-6913-49f1-b3e3-8bbb260741a5.png)

Oh! Notice we are getting a "Request timed out." Let us fix that. 

2. Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall, keep client-1 instance open. 

- Start Menu > Windows Defender Firewall with Advanced Secruity programme > Inbound Rules > Sort by Porotocol > 

- Enable "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In) Private and Domain Profiles. 2 Inbound Rules.

![Inkedvivaldi_Gb9rFL8rhC](https://user-images.githubusercontent.com/109401839/213214025-94b0bfb0-f017-4e8b-8676-d01ffeb9ab93.jpg)


3. Check back at Client-1 to see the ping succeed

![vivaldi_WbtokOOBck](https://user-images.githubusercontent.com/109401839/213214146-018e77d5-98a4-4256-91fd-16647ff58006.png)

Look at that beautiful traffic. Now its time to ... 

<h2>Install Active Directory<h2>



1. Login to DC-1 and install Active Directory Domain Services

- Server Manager > "Add Roles and Features" > Check "Active Directory Domain Services"

![vivaldi_od5BgUKG6G](https://user-images.githubusercontent.com/109401839/213214935-0fe230d0-60be-431a-bf31-53cfc50748b9.png)

2. Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)

![2023-01-18 09 37 20 coursecareers com a3928ff24e0f](https://user-images.githubusercontent.com/109401839/213215535-f43842d0-f1ab-4c6a-91d1-18d8a9bdff06.jpg)

![2023-01-18 09 38 10 coursecareers com 78e39ae4181d](https://user-images.githubusercontent.com/109401839/213215738-c6379380-e5b8-438b-95a8-6906a16ff339.jpg)

3. Restart and then log back into DC-1 as user: mydomain.com\labuser

![vivaldi_xJc36FTsPS](https://user-images.githubusercontent.com/109401839/213216324-dccbe8d1-3791-4eea-8609-6643d27f1bc9.png)
