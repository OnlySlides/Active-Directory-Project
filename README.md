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
4. Configure AD on Wins server and promote to DC, create new domain users, and join the target machine into the domain
5. Generate telemtry with Kali & ART by attacking one of the users in part 4. Guided project tool (Crowbar) was not working for so I used a different tool instead (Hydra). 

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
<br/>
<br/>

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

Time to install Splunk UF on target machine > Splunk.com > login > Products > Free trials and downloads > Universal Forwarder > Get my Free download > ensure on-premise Splunk Enterprise instance > set-up credentials (admin, generate random pw option checked) > no deployment server so skip > receiving indexer = Splunk server so input the Splunk IP, default port for Splunk to receive events is 9997 so leaving as is > Install. 
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

Any time inputs.conf file is updated, Splunk's UF need to be restarted. To restart Splunk's UF service: search Services in start menu > run as administrator > Search for Splunk Forwarder > scrolling to the right > notice NT SERVICE > double click on it > account might not be able to collect logs due to some of its permissions > select Local System Account instead > Apply and OK > Restart the service > OK if error 1503 occurs > Start service
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

If everything was set up properly, we should start seeing data coming in from the target machine. pps at the top left > Search & Reporting > search for the endpoint index > note timeframe (24 hours is fine in this instance) > immediately see events > see that the host is my Wins VM's pc name(BDEMOVM) > source types are the ones indicated in the inputs.conf file.
<p align="center">
Apps > Search & Reporting: <br/>
<img src="https://i.imgur.com/5E7LLPz.png" width="30%" alt="Active-Directory-Project"/>
<br/>
endpoint index search: <br/>
<img src="https://i.imgur.com/CqFgkia.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Host filter in search results: <br/>
<img src="https://i.imgur.com/Q6QIjYM.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Sources from inputs.conf file: <br/>
<img src="https://i.imgur.com/8SxUntD.png" width="40%" alt="Active-Directory-Project"/>
<br/>
<br/>

Time to install Sysmon & UF on my own on the ADDC server VM; steps should be the same as installing Sysmon & UF on the target machine. 

Need to change the IP address of the AD VM to match the one in diagram. Start AD VM > open CMD > "ipconfig" command to check current IP address > Network & Adapter Settings > IP address of 192.168.10.7, default gateway of 192.168.10.1, DNS of 8.8.8.8 > rename the machine to ADDC01 > Restart VM > device name has updated > open CMD to check updated IP address with "ipconfig" command > ping the Splunk server to ensure connectivity. 
<p align="center">
AD VM previous IP address: <br/>
<img src="https://i.imgur.com/nGzswys.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Update AD VM IP address: <br/>
<img src="https://i.imgur.com/xIttTgi.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Rename VM: <br/>
<img src="https://i.imgur.com/Ek4uUEa.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Device name updated: <br/>
<img src="https://i.imgur.com/84DdaZ7.png" width="35%" alt="Active-Directory-Project"/>
<br/>
IP address updated to match diagram: <br/>
<img src="https://i.imgur.com/DGfSj5z.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Ping Splunk server IP address: <br/>
<img src="https://i.imgur.com/Hq1TU9R.png" width="50%" alt="Active-Directory-Project"/>
<br/>

Install Splunk UF & Sysmon on the AD VM. Steps are literally the same as when it was installed on the target machine above. Sysmon steps are omitted again. <br/>

Splunk.com > login > Products > Free trials and downloads > Universal Forwarder > Get my Free download > ensure on-premise Splunk Enterprise instance > set-up credentials (admin, generate random pw option checked) > no deployment server so skip > receiving indexer = Splunk server so input the Splunk IP, default port for Splunk to receive events is 9997 so leaving as is > Install. <br/>
Inputs.conf file steps & removing NT SERVICE steps are the same as above. 
<p align="center">
Services > UF switch to Local System. Sysmon verified installed: <br/>
<img src="https://i.imgur.com/qNPwpSi.png" width="75%" alt="Active-Directory-Project"/>
<br/>
  
No need to enable splunk server to receive the data or create new index since that was done previously and the receiving port is the same (9997). If everything was installed & set-up correctly > refresh or search for the endpoint index again in Splunk > should now see 2 hosts.
<p align="center">
2 hosts with Splunk endpoint index search: <br/>
<img src="https://i.imgur.com/6E6F1qH.png" width="50%" alt="Active-Directory-Project"/>
<br/>
<br/>

#### Part 4: Install & configure AD on Wins server and promote to DC, create new domain users, and join the target machine into the newly created domain
Set a static IP address on the AD server (already done previously) > verify connectivity by pinging Google.com & the Splunk server; note that ping between Wins computers won't work as they block ICMP traffic by default. Head to Server Manager after verifying connectivity. 
<p align="center">
Ping Google.com & Splunk server: <br/>
<img src="https://i.imgur.com/Lhr2jCS.png" width="45%" alt="Active-Directory-Project"/>
<br/>

Server Manager > Manage at the top right corner > Add Roles and Features > Next > Select "Role-based or feature-based installation" in Installation Type > only have 1 server > Select Active Directory Domain Services (AD DS) in Server Roles > Add Features > Next > Next > Install
<p align="center">
Manage in Server Manager: <br/>
<img src="https://i.imgur.com/hBzohta.png" width="60%" alt="Active-Directory-Project"/>
<br/>
AD server as the destination server: <br/>
<img src="https://i.imgur.com/xdegttc.png" width="55%" alt="Active-Directory-Project"/>
<br/>
AD DS server role: <br/>
<img src="https://i.imgur.com/T7Ts2re.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Add AD DS features: <br/>
<img src="https://i.imgur.com/Pp3EQbG.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Confirm & install: <br/>
<img src="https://i.imgur.com/61ss45L.png" width="55%" alt="Active-Directory-Project"/>
<br/>
Install complete: <br/>
<img src="https://i.imgur.com/MarVRnR.png" width="50%" alt="Active-Directory-Project"/>
<br/>

After installation, configuration is required: flag icon beside Manage > "Promote this server to a DC" because we are creating a new domain > Add a new forest > blearn.local domain name > domain name must have a top level domain (can't be just blearn, must be blearn.#) > Next > leave everything default & input PW > leave things default & Next until Prerequisites Check > Install > Server will automatically restart after
<p align="center">
Promote the server to DC: <br/>
<img src="https://i.imgur.com/6rNcZej.png" width="45%" alt="Active-Directory-Project"/>
<br/>
New forest & domain name: <br/>
<img src="https://i.imgur.com/WHAL0VZ.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Install: <br/>
<img src="https://i.imgur.com/D59DyNc.png" width="60%" alt="Active-Directory-Project"/>
<br/>

After restart, the domain followed by backslash indicate successfull installation of AD DS and promotion of the server to Domain Controller. Next step is to create some users. <br/>

Login > In Server Manager top right > Tools > AD Users and Computers (where we can create objects) > expand our blearn.local domain and click on Builtin, these groups are automatically created by AD like the Administrators group. <br/>

In the real world, users are likely broken up into different organizational units like HR, Accounting, etc. To mimic that: right click domain > New > Organizational Unit (OU) > IT > OK. To create a new user within the IT unit, right click > New > User > input user details & credentials > lab environment so we are ignoring password policies > Finish. <br/>
Create another OU for HR and new user within HR: same steps as above. 
<p align="center">
Successful install of AD DS & promotion to DC: <br/>
<img src="https://i.imgur.com/bjuiKES.png" width=30%" alt="Active-Directory-Project"/>
<br/>
Server Manager AD Users & Computers to create objects: <br/>
<img src="https://i.imgur.com/GtaJosf.png" width="30%" alt="Active-Directory-Project"/>
<br/>
Example of Administrator group in Builtin: <br/>
<img src="https://i.imgur.com/RhzOvnW.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Create a new OU: <br/>
<img src="https://i.imgur.com/K14Jnny.png" width="40%" alt="Active-Directory-Project"/>
<br/>
New IT OU: <br/>
<img src="https://i.imgur.com/j6rSJhG.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Create a new user in IT OU: <br/>
<img src="https://i.imgur.com/eU9BA44.png" width="40%" alt="Active-Directory-Project"/>
<br/>
New user Jenny in IT OU: <br/>
<img src="https://i.imgur.com/b461WvF.png" width="40%" alt="Active-Directory-Project"/>
<br/>
<img src="https://i.imgur.com/zbD4JGq.png" width="40%" alt="Active-Directory-Project"/>
<br/>
New user Terry in new OU HR: <br/>
<img src="https://i.imgur.com/u3q7eg8.png" width="40%" alt="Active-Directory-Project"/>
<br/>

Time to join the new blearn.local domain to the Wins target machine using Jenny Smith's account. <br/>
On the Wins target machine, "PC" in Wins search > Properties > scroll down and select Advanced system settings >  Computer Name tab > Change > Select Domain instead of Workgroup > input domain > get an error because target machine does not know how to resolve blearn.local = DNS issue. <br/>
Bottom right network icon > Open Network and Internet settings > change adapter options > right click the adapter > Properties > double click IPv4 > pointing to Google's DNS > edit so it points to our DC hosted on 192.168.10.7 > OK > verify in CMD with "ipconfig /all" > DNS Servers address updated so it should now be good to join the domain. <br/>
Back in PC system properties > click on OK after inputting domain > prompted to enter credentials > use administrator account of the server to log in as it has the proper permissions > restart computer after > select "Other user" > log in with newly created user called Jenny Smith, username = jsmith > ensure Sign in to: is pointing to our domain
<p align="center">
Advanced system settings in PC Properties: <br/>
<img src="https://i.imgur.com/NQguMiU.png" width="20%" alt="Active-Directory-Project"/>
<br/>
Change domain in Computer Name: <br/>
<img src="https://i.imgur.com/BaY6yRd.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Domain instead of Workgroup & Error: <br/>
<img src="https://i.imgur.com/kl6Edkx.png" width="40%" alt="Active-Directory-Project"/>
<br/>
DNS server pointing to Google: <br/>
<img src="https://i.imgur.com/gLO9io1.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Edit DNS to point to DC hosted on 192.168.10.7: <br/>
<img src="https://i.imgur.com/C1Qtx1M.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Verify updated DNS address in CMD: <br/>
<img src="https://i.imgur.com/jKoHYRY.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Switch to Domain & input administrator credentials: <br/>
<img src="https://i.imgur.com/nnK0Oad.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Successful domain change: <br/>
<img src="https://i.imgur.com/NdfEUf0.png" width="30%" alt="Active-Directory-Project"/>
<br/>
Other user & log in with Jenny's credentials: <br/>
<img src="https://i.imgur.com/qx3p4oj.png" width="45%" alt="Active-Directory-Project"/>
<br/>
Successful domain join & domain user login: <br/>
<img src="https://i.imgur.com/ClszwMt.png" width="40%" alt="Active-Directory-Project"/>
<br/>

Part 4 completed as new users were created, the machine was joined to a new domain, and we logged in as a domain user.

#### Part 5: Utilize Kali Linux to perform brute force attack onto a user created in part 4 to view Telemetry via Splunk. Setup and Install ART (Atomic Red Team) to run Atomic tests.

On the Kali machine, static IP address needs to be set up. Start Kali VM > right click Ethernet Connection at the top right > Edit Connections > select the 1st profile "Wired connection 1" > click on cog icon at the bottom left > IPv4 Settings > set Method to Manual instead of DHCP > Add > input 192.168.10.250 as per the diagram for IP address > Netmask: 24 > Gateway: 192.168.10.1 > DNS: 8.8.8.8 > Save > Close
<p align="center">
Kali VM edit network connections: <br/>
<img src="https://i.imgur.com/NEUdENl.png" width="25%" alt="Active-Directory-Project"/>
<br/>
Wired Connection 1: <br/>
<img src="https://i.imgur.com/Ya3nGiu.png" width="30%" alt="Active-Directory-Project"/>
<br/>
Updated wired connection 1 info: <br/>
<img src="https://i.imgur.com/dztW9CU.png" width="60%" alt="Active-Directory-Project"/>
<br/>

Right click on the Kali desktop > Open Terminal Here > command "ip a" > if IP address is not correct > click on Ethernet icon at the top right of desktop > Disconnect > Click Ethernet icon again > select the Wired connection 1 profile > clear out the screen in terminal > "ip a" command to verify IP address > ping Google to verify connectivity. Update & upgrade repositories with command "sudo apt-get update && sudo apt-get upgrade -y".
<p align="center">
Check IP address: <br/>
<img src="https://i.imgur.com/TGLBKlY.png" width="55%" alt="Active-Directory-Project"/>
<br/>
Incorrect IP address so disconnect and reconnect: <br/>
<img src="https://i.imgur.com/14Ez4RH.png" width="25%" alt="Active-Directory-Project"/>
<br/>
Correct IP address now: <br/>
<img src="https://i.imgur.com/IeRGIsi.png" width="45%" alt="Active-Directory-Project"/>
<br/>
Ping Google to verify connectivity: <br/>
<img src="https://i.imgur.com/an3NWIv.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Update & upgrade repositories: <br/>
<img src="https://i.imgur.com/nH3A9Qn.png" width="50%" alt="Active-Directory-Project"/>
<br/>
<br/>
  
Ready to set up the attack by creating a new directory called ad-project with command "mkdir ad-product"; that creates a directory on the desktop and all files we create and use will be put in that directory. <br/>
Crowbar will be used as the attack tool used to perform brute-forced attacks that can target the DC or target machine. <br/>

Open terminal in Kali VM > command "mkdir ad-product" to create a new directory on the desktop > command "sudo apt-get install -y crowbar" to install the attack tool Crowbar > switch to a different directory where a popular wordlist is within Kali with command "cd /usr/share/wordlists/" > command "ls" to see the files within the 'wordlists' directory > notice the 'rockyou.txt.gz' file with gz(gunzip) extension > need to unzip the file using gunzip with command "sudo gunzip rockyou.txt.gz" > "ls" command again and gz extension should be gone > copy the file into the ad-project folder with command "cp rockyou.txt ~/Desktop/ad-project" > change into ad-project directory "cd ~/Desktop/ad-project" > "ls -lh" command to see file details.
<p align="center">
Create ad-project directory: <br/>
<img src="https://i.imgur.com/uqlFL9E.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Install Crowbar: <br/>
<img src="https://i.imgur.com/7CmP0oL.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Locate rockyou.txt.gz & unzip > copy file into ad-project directory: <br/>
<img src="https://i.imgur.com/3srpEyr.png" width="50%" alt="Active-Directory-Project"/>
<br/>
See file details: <br/>
<img src="https://i.imgur.com/wYR56XZ.png" width="60%" alt="Active-Directory-Project"/>
<br/>

The txt file contains a lot of passwords and not all is needed for this project: command "head -n 20 rockyou.txt" = only want the 1st 20 lines of the txt file pointing to rockyou.txt > see 20 lines of output > output the contents into a file called passwords.txt with command "head -n 20 rockyou.txt > passwords.txt", the passwords.txt file is automatically created > command "ls" and we should see a passwords.txt file > open the file by command "cat passwords.txt". <br/>
In this scenario, we want to target a certain password so let's edit the passwords list: command "nano passwords.txt" to edit > add the password at the bottom of the file > Ctrl X to save > Y > Enter > verify password addition by command "cat passwords.txt".
<p align="center">
First 20 lines of output: <br/>
<img src="https://i.imgur.com/Wf16MZW.png" width="40%" alt="Active-Directory-Project"/>
<br/>
Output content into new txt file > "cat passwords.txt" to check output: <br/>
<img src="https://i.imgur.com/U7RTPeo.png" width="40%" alt="Active-Directory-Project"/>
<br/>
New text file created: <br/>
<img src="https://i.imgur.com/nIyF0Ds.png" width="25%" alt="Active-Directory-Project"/>
<br/>
Add password into password.txt file: <br/>
<img src="https://i.imgur.com/K6qGMwA.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Verify password addition with "cat passwords.txt" command: <br/>
<img src="https://i.imgur.com/IlbBFYS.png" width="35%" alt="Active-Directory-Project"/>
<br/>

On the target machine, RDP needs to be enabled: search bar "PC" > Properties > scroll down to Advanced System settings > log in with admin account > Remote tab > Allow remote connections to this computer > select users > Add > "jsmith" Check Names > tsmith "Check Names" > OK > OK > Apply > OK to close it out > RDP is now enabled on target machine.
<p align="center">
Advanced system settings administrator login: <br/>
<img src="https://i.imgur.com/vrVXwJk.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Allow remote connections to this computer & Select Users: <br/>
<img src="https://i.imgur.com/6CVjSux.png" width="35%" alt="Active-Directory-Project"/>
<br/>
Add users: <br/>
<img src="https://i.imgur.com/2Hz9f5R.png" width="20%" alt="Active-Directory-Project"/>
<br/>
Add Jenny & Terry (previously created users in AD): <br/>
<img src="https://i.imgur.com/QKZFj1K.png" width="45%" alt="Active-Directory-Project"/>
<br/>
Addition successful: <br/>
<img src="https://i.imgur.com/8jCaz93.png" width="40%" alt="Active-Directory-Project"/>
<br/>

Back in the Kali VM, "crowbar -h" to look at the tool's details > we are interested in RDP > command "crowbar -b rdp -u tsmith -C passwords.txt -s 192.168.10.100/32" (-b to specify service, -u to specify account of interest, -C to specify the password list, -s to specify the source IP address in CIDR notation; 32 instead of 24 because we want to target this 1 IP address). <br/>
Crowbar was not working for me like in the guided project so I pivoted and used Hydra instead by looking at resources online for a similar command as above. Command "hydra -t 1 -V -f -l tsmith -P passwords.txt rdp://192.168.10.100". -t = task value, -V = verbose, -f = quit if successfully login, -l = user, -P = passwords.txt file, service = rdp://IP address. I believe there's an even easier command to limit the output but the command worked fine for the purposes of this project. 
<p align="center">
View Crowbar's details: <br/>
<img src="https://i.imgur.com/kQvofAO.png" width="50%" alt="Active-Directory-Project"/>
<br/>
Crowbar brute force not working: <br/>
<img src="https://i.imgur.com/ognBhWb.png" width="60%" alt="Active-Directory-Project"/>
<br/>
Used Hydra instead: <br/>
<img src="https://i.imgur.com/tpRKAMB.png" width="65%" alt="Active-Directory-Project"/>
<br/>

