This repository documents my very first hands-on experience with **Nmap** (Network Mapper) as part of authorized red team / ethical hacking training.

The goal is to build muscle memory with basic-to-intermediate scanning techniques in a controlled, legal lab environment.

All thanks to @parocyber on linkedIn

ensured nmap is installed 

Realized i am not on the preferred network for the ethical hacking course; decided to make good use of the knowledge gained and practice. i will not always be spoon fed. performed a simple nmap scan 

Downloaded the required resources and starting all over



# Nmap Practice Repository

## Disclaimer
This repository contains materials and outputs from a structured educational lab focused on ethical hacking and network security scanning. 
All activities documented here were performed in a controlled, authorized learning environment using intentionally vulnerable test systems or resources provided for educational purposes only.
No real-world systems were targeted without explicit permission. The content is for academic and skill-development use only and should not be applied to any system without proper authorization.

## Objectives
In this repository, I documented my practice and lab exercises using Nmap for network discovery and security scanning.

## Required Resources
- Kali VM customized for the Ethical Hacker course
- Internet access 
- Authorized target systems or lab environments 

## Lab Exercises
## TOPOLOGY

<img width="1146" height="663" alt="1 topology" src="https://github.com/user-attachments/assets/55d94ac1-a482-4140-863e-e419900823ad" />


## Part 1: Investigate Nmap

### Step 1: Log into Kali Linux and Verify the Environment
I logged into the Kali system with the username `kali` and the password `kali`. I was presented with the Kali desktop.

I opened a terminal window.

I verified that Kali has an interface in the `10.6.6.0/24` network using the `ifconfig` command.

I used the `nmap -V` command to verify that Nmap is installed and to display the Nmap version.

**Command:**
nmap -V

<img width="948" height="933" alt="nmap verification" src="https://github.com/user-attachments/assets/c05c7151-b78f-4a34-85aa-628ed74729e8" />

### Step 2: Investigate Nmap Options and Features
I used the command `nmap` without specifying any options or targets. This returned a list of commonly used Nmap options.

To access the Nmap help system, I used the command:

**Command:**
nmap -h
man nmap

<img width="953" height="937" alt="2 nmap help page" src="https://github.com/user-attachments/assets/4d5cb9ce-9897-4bb1-8ddb-0ee619ce6625" />


<img width="946" height="942" alt="3 man page" src="https://github.com/user-attachments/assets/e074e83d-f3bd-4adc-96a6-27ffb2fcfcfb" />


I reviewed the detailed documentation in the man page. To exit the man page, I pressed q to quit and return to the terminal prompt.


## Part 2: Perform Basic Nmap Scans

### Step 1: Initiate a Basic Nmap Scan of the Target Computer
To quickly scan the DMZ for active hosts, I performed a discovery scan.
In a discovery scan, the scanning host sends an ICMP echo request (ping), a TCP SYN to port 443, a TCP ACK to port 80, and an ICMP timestamp request.
A response to any of the requests indicates that the host is up and the IP protocol stack on the host is functioning.

I entered the following command to scan the DMZ network:

**Command:**
nmap -sn 10.6.6.0/24

<img width="921" height="320" alt="4 basic scan" src="https://github.com/user-attachments/assets/3e340c47-e4a5-4998-adf7-01fa45f36a3e" />

The exact hosts discovered may vary depending on the lab environment, but the scan successfully identified 7 live hosts in the 10.6.6.0/24 network.


### Step 2: Perform a Default Nmap Scan on the Suspicious Host
The host `10.6.6.23` was identified as suspicious in a Wireshark capture.
it was necessary to perform additional reconnaissance to discover more about the computer and its services.

I used the `nmap` command to execute a default scan on the target host.

**Command:**
nmap 10.6.6.23

<img width="933" height="272" alt="5 suspicious host scan" src="https://github.com/user-attachments/assets/7b350904-3525-4da6-9467-aab83dbc72e2" />

The scan revealed numerous open ports, indicating that this host is running many services and is likely a vulnerable machine

### Step 3: Perform OS Detection on the Target Host
The `-O` option can be used to further determine information about the operating system running on the target host. 
Some Nmap options require additional permissions and must be run as root or using the `sudo` command.

To find operating system information on the target host, I used the `nmap -O` command. I entered the password when prompted.

**Command:**
sudo nmap -O 10.6.6.23

<img width="945" height="407" alt="6 finding host OS" src="https://github.com/user-attachments/assets/efe2e7c6-0c86-424f-b42d-d5362383ac85" />


The scan successfully detected that the target is running Linux


### Step 2: Obtain Additional Information About the Host and Services
To provide additional information about the target computer.
it is possible to combine different options into a single command line.
The previous command identified several potentially open ports on the `10.6.6.23` host.

I used `-v`, `-p`, and `-sV` to find additional information about the services running on the open ports.
This command provided information about the FTP service running on port 21 on the target in verbose mode, with the timing set to fast (`-T4`):

**Command:**
nmap -v -p21 -sV -T4 10.6.6.23

<img width="947" height="496" alt="7 ftp version" src="https://github.com/user-attachments/assets/8ad7f17d-5f06-403f-8ec8-4dd4c73ace50" />

The scan revealed that port 21 is running vsftpd 3.0.3, a potentially vulnerable version of the FTP server


### Step 3: Perform an Intensive Scan on the FTP Service
The `-A` option executes OS detection, version detection, script scanning, and traceroute.
The `-A` scan can be very intrusive and therefore will be detected by many IDS systems.

To gather more information regarding the FTP service, I entered the command:

**Command:**
nmap -p21 -sV -A 10.6.6.23

<img width="932" height="507" alt="8 -A scan" src="https://github.com/user-attachments/assets/a7812a2a-8d2f-4372-9e66-306e6d7567d0" />

### Step 3: Investigate SMB Services with Scripts
The Server Message Block (SMB) protocol is a network file sharing protocol supported on Windows computers and by SAMBA on Linux. 
SMB enables applications to read and write files or request services over a network.
Open public shares or shared devices such as print servers on a network can be accessed through SMB.

The earlier scan of open ports on the target computer indicated that the SMB ports 139 and 445 are open.

I found more information on these ports using the `-A` and `-p` command options. The `-A` option executes several functions, including running the default scripts. I specified more than one port to scan by listing them separately with a comma between them.

**Command:**
nmap -A -p139,445 10.6.6.23

<img width="921" height="582" alt="9 SMB" src="https://github.com/user-attachments/assets/b21e0ecf-c432-4f9b-9ddb-92624f1ff107" />

The scan confirmed that Samba -Debian is running on both ports

### Step 4: Use Nmap Scripting Engine (NSE) for SMB Enumeration
Nmap contains the powerful Nmap Scripting Engine (NSE)
It enables the programming of various Nmap options and conditional actions to be taken as a result of the responses.
NSE has built-in scripts that enumerate users, groups, and network shares.

One of the more commonly used scripts for SMB discovery is the `smb-enum-users.nse` script.

I used the Nmap NSE script with the command:

**Command:**
nmap --script smb-enum-users.nse -p139,445 10.6.6.23

<img width="950" height="447" alt="10 usernames uncovered" src="https://github.com/user-attachments/assets/d9c07a08-437a-4e78-8186-98984e419dd4" />

The script successfully enumerated multiple user accounts on the target Samba server, including common accounts like arbiter and masterchief

### step 5: Continuing the use of NSE
I enumerated network shares using another NSE script 

**Command**
nmap --script smb-enum-shares.nse -p445 10.6.6.23

<img width="952" height="665" alt="11 enum shares" src="https://github.com/user-attachments/assets/48cb70ca-438d-4a4b-a513-a3d639d643a9" />

In the output, share names that end with a “$” character represent hidden shares that include system and administrative shares.




















































