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
<img src="https://i.imgur.com/h3za37m.png" width="25%" alt="Active-Directory-Project"/>
<br/>
Duplicating icons: <br/>
<img src="https://i.imgur.com/ZoaYXgm.png" width="15%" alt="Active-Directory-Project"/>
<br/>
Complete logical diagram: <br/>
<img src="https://i.imgur.com/yh04pcG.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Information within the diagram will be referenced throughout the project. 

#### Part 2: Install VMs on VirtualBox (VB); Wins 10, Kali Linux, Ubuntu Server, & Wins Server 2022
Wins 10 & Kali Linux is already set up on VB so steps will not be shown below. <br/>
For Wins server 2022: download iso image from [Microsoft](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022) > input free trial info > 64-bit edition > add into VM on VB > typical new VM set-up > start VM to set-up the OS > desktop experience is the GUI (non-desktop experience = CLI only) > input password > log in > Server Manager should automatically open. The Server Manager will be used to install AD. 
<p align="center">
ISO 64-bit image download: <br/>
<img src="https://i.imgur.com/dzthvxU.png" width="55%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/ZkgGToZ.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Create VM: <br/>
<img src="https://i.imgur.com/IZ3fAEo.png" width="50%" alt="Active-Directory-Project"/>
<br/>
VM configuration summary: <br/>
<img src="https://i.imgur.com/Ry7fMQs.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Start the VM & install the OS: <br/>
<img src="https://i.imgur.com/VxeEl2k.png" width="40%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/nBljPA9.png" width="45%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/TNbRahD.png" width="30%" alt="Active-Directory-Project"/>
<br/>
Server Manager: <br/>
<img src="https://i.imgur.com/mQa1mMa.png" width="80%" alt="Active-Directory-Project"/>
<br/>
<br/>

Need to install Splunk server: download at https://ubuntu.com/server > set-up the Splunk VM to have higher processing capabilities compared to other VMs because it will be ingesting data and running searches > start the VM > 1st option > DONE on most of the configuration default options > input account credentials > reboot > login > run a command to update and upgrade all repositories > server is good to go after restart > take a snapshot of the VM. 
<p align="center">
Ubuntu server download: <br/>
<img src="https://i.imgur.com/D0UD5CX.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Splunk VM set-up with higher RAM & disk size: <br/>
<img src="https://i.imgur.com/Edx4K1u.png" width="50%" alt="Active-Directory-Project"/>
<br/>
1st option: <br/>
<img src="https://i.imgur.com/WhhZktV.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Account credentials: <br/>
<img src="https://i.imgur.com/bniAnt6.png" width="40%" alt="Active-Directory-Project"/>
<br/>
After logging into the screen below, reboot machine: <br/>
<img src="https://i.imgur.com/iKs7szc.png" width="55%" alt="Active-Directory-Project"/>
<br/>
Update and upgrade all repositories command, reboot machine again: <br/>
<img src="https://i.imgur.com/Rlv20hH.png" width="55%" alt="Active-Directory-Project"/>
<br/>
Server is now fully updated: <br/>
<img src="https://i.imgur.com/hS5IWKZ.png" width="55%" alt="Active-Directory-Project"/>
<br/>

We now have 4 VMs installed.
<p align="center">
4 VMs: <br/>
<img src="https://i.imgur.com/bE8F2hi.png" width="35%" alt="Active-Directory-Project"/>
<br/>

#### Part 3: Install Sysmon & Splunk onto Wins target machine & Wins server
Create NAT network so VMs can be on the same network but still have internet access. In VB > Tools > Network > NAT Networks > Create > edit IP address to the Network IP address in the diagram > Apply. <br/>
Navigate to Splunk VM settings to change network settings to NAT network. Right click on the Splunk machine > Settings > Network > Attached to NAT Network > elect the Nat Network that was just created. <br/>
Do the same for all the other VMs (ADDC01, Kali, Windows); not shown below but the steps are the same as above. 
<p align="center">
NAT Network creation with IP address of 192.168.10.0/24: <br/>
<img src="https://i.imgur.com/IIy9s4y.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Attaching NAT Network to Splunk VM: <br/>
<img src="https://i.imgur.com/UYfR6DV.png" width="60%" alt="Active-Directory-Project"/>
<br/>
<br/>
  
Need to set up a static IP address of 192.168.10.10 for the Splunk server to match our diagram. <br/>
Start Splunk server VM > "ip a" command to check IP address of the server > IP address is not what we want, "sudo nano /etc/netplan/00-installer-config.yaml" command > using arrow keys, replace 'true' with 'no' from dhcp4 indicating we do not want DHCP > press Enter key > press Tab key 3 times > input "addresses: [192.168.10.10/24] > press Enter key & Tab key 3 times > input "nameservers: "(spaces after the colon are intentional) > press Enter key & Tab key 5 times > input "addresses: [8.8.8.8]" which is Google's DNS IP address > press Enter key & Tab key 3 times > input "routes: " > press Enter key & Tab key 5 times > input "- to: default" to indicate default route > press Enter key & Tab key 6 times > input "via: 192.168.10.1" which is the gateway. Save configuration by holding Ctrl X > Y > Enter key. 
<p align="center">
Check IP address of Splunk server: <br/>
<img src="https://i.imgur.com/qDD3abO.png" width="55%" alt="Active-Directory-Project"/>
<br/>
Default network configuration: <br/>
<img src="https://i.imgur.com/1OWU34A.png" width="25%" alt="Active-Directory-Project"/>
<br/>
Updating network configuration pt. 2: <br/>
<img src="https://i.imgur.com/SfTvd4w.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Save configuration: <br/>
<img src="https://i.imgur.com/OypzBgL.png" width="40%" alt="Active-Directory-Project"/>
<br/>
  
Clear the screen > "sudo netplan apply" command > "ip a" to check IP address > IP address now matches the one stated in the diagram. Ping Google to verify connection. 
<p align="center">
Verify network changes & connectivity: <br/>
<img src="https://i.imgur.com/bacD42W.png" width="80%" alt="Active-Directory-Project"/>
<br/>
<br/>

Time to install Splunk. On the host machine > navigate to Splunk.com > sign-up for a Splunk account > Products > Free trials and downloads > Splunk Enterprise > get free Trial > Linux OS > download .deb file.
<p align="center">
Splunk Free Trials & Downloads: <br/>
<img src="https://i.imgur.com/2q5pd7S.png" width="30%" alt="Active-Directory-Project"/>
<br/>
Splunk Enterprise free trial: <br/>
<img src="https://i.imgur.com/0cZRT4L.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Linux OS .deb file: <br/>
<img src="https://i.imgur.com/FjL8N04.png" width="70%" alt="Active-Directory-Project"/>
<br/>

Head back to Splunk VM command line to install guest add-ons for VB > command "sudo apt-get install virtualbox (Tab key to see options), interested in the guest additions iso, full command "sudo apt-get install virtualbox-guest-additions-iso" > Y > Enter for default services to be restarted. Packages should be installed now. 
<p align="center">
Install & update additions iso: <br/>
<img src="https://i.imgur.com/B49QISu.png" width="100%" alt="Active-Directory-Project"/>
<br/>

Add a shared folder in the Splunk VM where Splunk Enterprise was downloaded on the host machine: at the top of the Splunk VM window > Devices > Shared Folders > Settings > Add folder at the right side > input folder path, folder name, check the Read-only, Auto-mount, Make Permanent > OK. 
<p align="center">
Shared folder settings: <br/>
<img src="https://i.imgur.com/fUTCHIy.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Folder path, name, & configs: <br/>
<img src="https://i.imgur.com/BxM0NbI.png" width="30%" alt="Active-Directory-Project"/>
<br/>
Shared folder added: <br/>
<img src="https://i.imgur.com/ZgXQTyl.png" width="60%" alt="Active-Directory-Project"/>
<br/>

