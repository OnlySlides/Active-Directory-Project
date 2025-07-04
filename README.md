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
Navigate to draw.io > search for a server icon and duplicate it, 1 for Splunk & 1 for AD > search for computer icon and duplicate it, 1 for target machine & 1 for attacker machine > change colour of attacker machine to red
