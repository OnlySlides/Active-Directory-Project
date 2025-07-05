# Active-Directory-Project

## Objective
Build a fully functional domain environment on premise by creating a logical diagram, installing & configuring: Active Directory (AD), Splunk, Windows (Wins) machine & Kali Linux machine. This guided project provides a basic understanding of how a domain environment works, how to ingest events to a Security Information and Event Management (SIEM), and generate telemetry related to attacks. 

Credit for this guided project goes to [MyDfir on YouTube](https://www.youtube.com/watch?v=mWqYyl89QaY). 

### Skills Learned
- Creating a logical diagram
- Installing & configuring software/assets/virtual machines (VMs) including: Spunk, Wins server 2022 (AD), Wins 10 (target machine), Kali (attacking machine), Splunk (Ubuntu server 22.04)
- Configuring AD server & promoting it to a Domain Controller (DC), creating new domain users, & joining the target machine into the domain
- Generating telemetry with Kali & Atomic Red Team (ART) by attacking one of the users

### Tools Used
- draw.io to create the logical diagram
- Wins server as the AD
- Splunk universal forwarder to send data back to the Splunk server
- ART to generate the attack telemetry 
- Sysmon installed on the AD server and target machine to view the attack telemetry 

### Takeaways (CHANGE IF NEEDED OR REMOVE IF UNNECESSARY)
This guided project is a great example of why it is so important to understand a multitude of tools and how they intersect in order to achieve a certain result. During my self-study on HackTheBox, they use Splunk as the primary SIEM so the introduction to Microsoft's SIEM in this lab was interesting but also overwhelming. With little knowledge in PS, retrieving the specific data for the report would not have been possible without the provided script. 

### Steps
This project will be split into 5 parts: 
1. Build/draw a logical diagram
2. Install & configure Wins server 2022 (AD), Wins 10 (target machine), Kali (attacking machine), Splunk (Ubuntu server 22.04)
3. Install and configure Sysmon & Splunk
4. Configure AD on Wins server and promote to DC, create new domian users, and join the target machien into the domain
5. Generate telemtry with Kali & ART by attacking one of the users in part 4.

#### Part 1: Create & Map out a Logical Diagram
Navigate to draw.io > search for a server icon and duplicate it, 1 for Splunk & 1 for AD > search for computer icon and duplicate it, 1 for target machine & 1 for attacker machine > change colour of attacker machine to red. <br/>
Get icons for a Switch, a Router, & a Cloud to indicate the internet. Connect the icons. <br/>
Dotted green lines indicate traffic being forwarded to the Splunk server. <br/>
Add text boxes for more information within the diagram for each icon if applicable. 
<p align="center">
Searching for icons: <br/>
<img src="https://i.imgur.com/h3za37m.png" height="25%" width="25%" alt="Active-Directory-Project"/>
<br/>
Duplicating icons: <br/>
<img src="https://i.imgur.com/ZoaYXgm.png" height="15%" width="15%" alt="Active-Directory-Project"/>
<br/>
Complete logical diagram: <br/>
<img src="https://i.imgur.com/yh04pcG.png" height="60%" width="60%" alt="Active-Directory-Project"/>
<br/>
Information within the diagram will be referenced throughout the project. 

#### Part 2: Install VMs on VirtualBox (VB); Wins 10, Kali Linux, Ubuntu Server, & Wins Server 2022
Wins 10 & Kali Linux is already set up on VB so steps will not be shown below. <br/>
For Wins server 2022: download iso image from [Microsoft](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022) > input free trial info > 64-bit edition > add into VM on VB > typical new VM set-up > start VM to set-up the OS > desktop experience is the GUI (non-desktop experience = CLI only) > input password > log in > Server Manager should automatically open. The Server Manager will be used to install AD. 
<p align="center">
ISO 64-bit image download: <br/>
<img src="https://i.imgur.com/dzthvxU.png" height="55%" width="55%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/ZkgGToZ.png" height="40%" width="40%" alt="Active-Directory-Project"/>
<br/>
Create VM: <br/>
<img src="https://i.imgur.com/IZ3fAEo.png" height="50%" width="50%" alt="Active-Directory-Project"/>
<br/>
VM configuration summary: <br/>
<img src="https://i.imgur.com/Ry7fMQs.png" height="50%" width="50%" alt="Active-Directory-Project"/>
<br/>
Start the VM & install the OS: <br/>
<img src="https://i.imgur.com/VxeEl2k.png" height="40%" width="40%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/nBljPA9.png" height="45%" width="45%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/TNbRahD.png" height="30%" width="30%" alt="Active-Directory-Project"/>
<br/>
Server Manager: <br/>
<img src="https://i.imgur.com/mQa1mMa.png" height="80%" width="80%" alt="Active-Directory-Project"/>
<br/>

Need to install Splunk server: download at https://ubuntu.com/server > set-up the Splunk VM to have higher processing capabilities compared to other VMs because it will be ingesting data and running searches > start the VM > 1st option > DONE on most of the configuration default options > input account credentials > reboot > login > run a command to update and upgrade all repositories > server is good to go after restart > take a snapshot of the VM. 
<p align="center">
Ubuntu server download: <br/>
<img src="https://i.imgur.com/D0UD5CX.png" height="50%" width="50%" alt="Active-Directory-Project"/>
<br/>
Splunk VM set-up with higher RAM & disk size: <br/>
<img src="https://i.imgur.com/Edx4K1u.png" height="50%" width="50%" alt="Active-Directory-Project"/>
<br/>
1st option: <br/>
<img src="https://i.imgur.com/WhhZktV.png" height="50%" width="50%" alt="Active-Directory-Project"/>
<br/>
Account credentials: <br/>
<img src="https://i.imgur.com/bniAnt6.png" height="40%" width="40%" alt="Active-Directory-Project"/>
<br/>
After logging into the screen below, reboot machine: <br/>
<img src="https://i.imgur.com/iKs7szc.png" height="55%" width="55%" alt="Active-Directory-Project"/>
<br/>
Update and upgrade all repositories command, reboot machine again: <br/>
<img src="https://i.imgur.com/Rlv20hH.png" height="55%" width="55%" alt="Active-Directory-Project"/>
<br/>
Server is now fully updated: <br/>
<img src="https://i.imgur.com/hS5IWKZ.png" height="55%" width="55%" alt="Active-Directory-Project"/>
<br/>

We now have 4 VMs installed.

#### Part 3: Install Sysmon & Splunk onto Wins target machine & Wins server
