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
- Splunk universal forwarder(UF) to send data back to the Splunk server
- ART to generate the attack telemetry 
- Sysmon installed on the AD server and target machine to view the attack telemetry 

### Takeaways (CHANGE IF NEEDED OR REMOVE IF UNNECESSARY)
This guided project is a great example of why it is so important to understand a multitude of tools and how they intersect in order to achieve a certain result. During my self-study on HackTheBox, they use Splunk as the primary SIEM so the introduction to Microsoft's SIEM in this lab was interesting but also overwhelming. With little knowledge in PS, retrieving the specific data for the report would not have been possible without the provided script. 
-------------------------
Halfway thorugh, the Windows machine could not connect to the Splunk server while following along the guided project. I troubleshooted the issue for a couple hours on my own and it ended up working after. This felt like a big win for me.

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

Back on the Splunk VM, reboot the machine with "sudo reboot" command and log back in after. <br/>
Add user (yourself) to the vboxsf group: after logging back in > "sudo adduser (your username) vboxsf" command. 
<p align="center">
Add user to vboxsf group: <br/>
<img src="https://i.imgur.com/dNOqI8K.png" width="40%" alt="Active-Directory-Project"/>
<br/>

Create a new directory called 'share': "mkdir share" command > "ls" command > see the folder in blue. We want to mount the shared folder on to the directory called Share: "sudo mount -t vboxsf -o uid=1000,gid=1000 Downloads share/" command > "ls -la" command to see share folder is now highlighted > change directories with "cd share/" command > "ls -la" command to view files in my Downloads folder including the Splunk installer (not shown in image)
<p align="center">
Create new directory called "share": <br/>
<img src="https://i.imgur.com/pd8J0bY.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Mount shared folder onto the directory "share": <br/>
<img src="https://i.imgur.com/iVk04UP.png" width="60%" alt="Active-Directory-Project"/>
<br/>
share/Downloads folder files: <br/>
<img src="https://i.imgur.com/SH7cK2C.png" width="60%" alt="Active-Directory-Project"/>
<br/>

To install Splunk: "sudo dpkg -i splunk(tab key)" Enter. Complete = Splunk install complete. Change into the direcotry of where Splunk is located on our server: "cd opt/splunk" command > "ls -la" command, notice all users and groups belong to Splunk which is a good thing that limit permissions to the Splunk user > change into the Splunk user "sudo -u splunk bash" ENTER > "cd bin" to change into bin direcotry > files listed in bin are all binaries that Splunk can use > binary used is ./splunk so "./splunk start" ENTER to run installer > general terms and license agreement > hold Enter to view > set up username & password > wait for completion > installation complete. 
<p align="center">
Splunk install command: <br/>
<img src="https://i.imgur.com/MIi0JM4.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Change into Splunk directory: <br/>
<img src="https://i.imgur.com/u40Awdk.png" width="70%" alt="Active-Directory-Project"/>
<br/>
Change to Splunk user, bin directory, start installer : <br/>
<img src="https://i.imgur.com/UEGMW5Z.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Licenses & Terms; hold Enter to view next lines: <br/>
<img src="https://i.imgur.com/KgAtyta.png" width="55%" alt="Active-Directory-Project"/>
<br/>
After setting up username & password, installation complete: <br/>
<img src="https://i.imgur.com/Fn9lE2j.png" width="60%" alt="Active-Directory-Project"/>
<br/>

Run 1 more command to ensure Splunk starts up every time the VM reboots: command "exit" to exit out of the user Splunk > command "cd bin" to change directory to bin > "sudo ./splunk enable boot-start -user splunk" command tells Splunk to run with the user Splunk whenever the VM reboots.
<p align="center">
User specific reboot command: <br/>
<img src="https://i.imgur.com/h9x66fz.png" width="70%" alt="Active-Directory-Project"/>
<br/>
<br/>

Time to install Splunk UF & Sysmon on the target machine and server; the process is very similar for the installations on both machines. <br/>
On the target machine, update IP address. Open CMD > "ipconfig" to check IP address > change IP address > network icon at bottom right of desktop > Open Network & Internet Settings > Change adapter options > right click > properties > IPv4 > properties > use the following IP address option to set a static IP > IP Address: 192.168.10.100, Subnet: 255.255.255.0, default gateway: 192.168.10.1 > Preferred DNS server: 8.8.8.8 > OK > close > open CMD > "ipconfig" > results should have the updated IP address so there are no IP conflicts. 
<p align="center">
Initial ipconfig: <br/>
<img src="https://i.imgur.com/SHtu0IL.png" width="60%" alt="Active-Directory-Project"/>
<br/>
IPv4 adapter properties: <br/>
<img src="https://i.imgur.com/B5mbEf9.png" width="40%" alt="Active-Directory-Project"/>
<br/>
IP addresses according to diagram: <br/>
<img src="https://i.imgur.com/tdfdjrt.png" width="40%" alt="Active-Directory-Project"/>
<br/>
New static IP address: <br/>
<img src="https://i.imgur.com/FvAqC4H.png" width="60%" alt="Active-Directory-Project"/>
<br/>

With a static IP address, open web browser on the target machine to try accessing Splunk server > 192.168.10.10:8000 (Splunk listens on port 8000) > not connecting for me so let's troubleshoot (took me 2 hours) > likely firewall issue so adding port 8000 in the Windows Defender firewall inbound & outbound rules > in Splunk server firewall (ufw), added traffic from Windows machine IP address to port 8000 for any protocols > works > big win. 
<p align="center">
192.168.10.10:8000: <br/>
<img src="https://i.imgur.com/pLVyuqw.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Unable to connect: <br/>
<img src="https://i.imgur.com/Z0wtRkz.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Add port 8000 to Wins firewall inbound & outbound: <br/>
<img src="https://i.imgur.com/Hx2DfHw.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Update ufw rules: <br/>
<img src="https://i.imgur.com/3u2r4pQ.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Connection successful after: <br/>
<img src="https://i.imgur.com/SZ4MgZK.png" width="70%" alt="Active-Directory-Project"/>
<br/>
<br/>

Time to install Splunk UF on target machine > Splunk.com > login > Products > Free trials and downloads > Universal Forwarder > Get my Free download > ensure on-premise Splunk Enterprise instance > set-up credentials (admin, generate random pw option checked) > no deployment server so skip > receiving indexer = Splunk server so input our Splunk IP, default port for Splunk to receive events is 9997 so leaving as is > Install. 
<p align="center">
Universal Forwarder on Splunk.com: <br/>
<img src="https://i.imgur.com/pi7JgAa.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Specific UF download file: <br/>
<img src="https://i.imgur.com/o9iUu6x.png" width="70%" alt="Active-Directory-Project"/>
<br/>
Open file after download is finished: <br/>
<img src="https://i.imgur.com/OpKtd4C.png" width="40%" alt="Active-Directory-Project"/>
<br/>
On-premise Splunk Enterprise instance: <br/>
<img src="https://i.imgur.com/A13wHKb.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Receiving indexer=Splunk server IP: <br/>
<img src="https://i.imgur.com/iWtBryn.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Install & Finish: <br/>
<img src="https://i.imgur.com/emR85uP.png" width="50%" alt="Active-Directory-Project"/>
<br/>
<br/>

Need to instruct Splunk forwarder on what we want to send to the Splunk server. <br/>
On the target machine, configuration file called inputs.conf > file path: C:\Program Files\SplunkUniversalForwarder\etc\system\default > copy the contents of the inputs.conf file into an administrator privilege Notepad > the content instructs the Splunk forwarder to push events related to Application, Security, System, and Sysmon over to the Splunk server > notice index = endpoint; events that fall under the categories (Application, Security, System, etc) will be sent over to Splunk and placed under the index "Endpoint",  if Splunk server does not have an index named Endpoint, it will not receive any of the events > save the Notepad in local directory (C:\Program Files\SplunkUniversalForwarder\etc\system\local). 
<p align="center">
inputs.conf file: <br/>
<img src="https://i.imgur.com/l2Atb3p.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Copy & paste inputs.conf file contents into a new Notepad: <br/>
<img src="https://i.imgur.com/1g1pUf7.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Save new file in local directory: <br/>
<img src="https://i.imgur.com/P1wfFs2.png" width="50%" alt="Active-Directory-Project"/>
<br/>

Point of creating a new file in a different directory is because we do not want to edit the default directory as that serves as a backup. 

Anytime inputs.conf file is updated, Splunk's UF need to be restarted. To restart Splunk's UF service: search Services in start menu > run as administrator > Search for Splunk Forwarder > scrolling to the right > notice NT SERVICE > double click on it > account might not be able to collect logs due to some of its permissions > select Local System Account instead > Apply and OK > Restart the service > OK if error 1503 occurs > Start service
<p align="center">
Splunk Forwarder Services > NT SERVICE: <br/>
<img src="https://i.imgur.com/CYOIf6d.png" width="70%" alt="Active-Directory-Project"/>
<br/>
NT SERVICE account: <br/>
<img src="https://i.imgur.com/k5q7eXk.png" width="45%" alt="Active-Directory-Project"/>
<br/>
Select Local System account instead: <br/>
<img src="https://i.imgur.com/Rt5gePg.png" width="45%" alt="Active-Directory-Project"/>
<br/>
Local System now: <br/>
<img src="https://i.imgur.com/hsUVMS4.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Restart the service: <br/>
<img src="https://i.imgur.com/S4durA0.png" width="25%" alt="Active-Directory-Project"/>
<br/>
Error is fine > Ok: <br/>
<img src="https://i.imgur.com/SCYJdUV.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Start the service: <br/>
<img src="https://i.imgur.com/2XaQEwI.png" width="25%" alt="Active-Directory-Project"/>
<br/>

Install Sysmon with Olaf configuration file. This is already completed on this machine so steps will not be displayed below. There are a multitude of resources out there on how to install Sysmon. <br/>
Splunk UF and Sysmom installed.

Time to finalize Splunk server configurations. In Splunk web portal > login > Settings > Indexes > this is where we create the ENDPOINT index mentioned in the inputs.conf file > New index > name it > Save 
<p align="center">
Settings at the top of Splunk homepage: <br/>
<img src="https://i.imgur.com/L7aMbre.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Indexes > New Index: <br/>
<img src="https://i.imgur.com/E3YuvWt.png" width="70%" alt="Active-Directory-Project"/>
<br/>
New endpoint index: <br/>
<img src="https://i.imgur.com/hrfo2lm.png" width="70%" alt="Active-Directory-Project"/>
<br/>

We then need to enable Splunk server to receive the data > Settings > Forwarding and receiving > Configure receiving > New Receiving Port > 9997 since that was the default port inputted when setting up the forwarder > Save
<p align="center">
Settings > Forwarding and receiving: <br/>
<img src="https://i.imgur.com/ejo8x84.png" width="45%" alt="Active-Directory-Project"/>
<br/>
COnfigure receiving: <br/>
<img src="https://i.imgur.com/OK6viOe.png" width="45%" alt="Active-Directory-Project"/>
<br/>
Input new receiving port and Save: <br/>
<img src="https://i.imgur.com/2r3xdc0.png" width="65%" alt="Active-Directory-Project"/>
<br/>
Successful port addition: <br/>
<img src="https://i.imgur.com/oMUjzaE.png" width="65%" alt="Active-Directory-Project"/>
<br/>
