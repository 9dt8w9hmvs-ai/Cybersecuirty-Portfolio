 Active Directory Home Lab

## What I built

I have started building a small Active Directory lab in VMware Workstation so I can get more hands-on experience with Windows security, Active Directory, logging and eventually SOC-style investigations.

At the moment the lab is completely isolated from my normal home network.

My current setup is:

| System | Role | IP |
|---|---|---|
| SERVER01 | Windows Server 2022 Domain Controller / DNS | 192.168.50.10 |
| WIN11-01 | Windows 11 Pro workstation | 192.168.50.20 |
| Kali Linux | Testing machine | To be confirmed |

The lab is running on a VMware Host-Only network called `VMnet2`.

```text
Network: 192.168.50.0/24
Gateway: None
VMware DHCP: Disabled
Internet Access: None

#**SERVER01**

I installed a version of Windows Server 2022 and configured it as my Domain Controller for the purpose of my Testing and server work.
The domain name i created is as follows:

Cyberlab.local

#**SERVER01 is using:**

Hostname: SERVER01
IP: 192.168.50.10
DNS: 192.168.50.10

I installed Active Directory and DNS
After setting it up i tested DNS using the following commands in command prompt:
nslookup cyberlab.local
nslookup SERVER01.cyberlab.local

These all confirmed and resolved back to SERVER01

#**WIN11-01**

I then built my primary Win11 virtual machine which will act as my main testing domain workstation.
I configured it using the following settings.

Hostname: WIN11-1
IP: 192.168.50.20
Subnet: 255.255.255.0
Gateway: None
DNS: 192.168.50.10

Before joining it to the domain i firstly tested connectivity by using the Ping command and used nslookup to confirm it could resolve the domain using DNS.

Once that was working i joined it to Cyberlab.local and confirmed it worked by logging in as domain admin


#**PROBLEMS I ENCOUNTERED**

I ran into a few issues during my setup of the virtual servers.
The first issue being VMWARE DHCP. When i first connected to WIN11-01 to VMnet2 it recieved a IP address in which I didn't want.
The IP address was 192.168.50.2

I troubleshooted by shutdowning the virtual machine and heading over to VMware adapter settings where i noticed Local DHCP was enabled on VMnet2

I disabled this as i wanted to use a static IP address for my lab machines.

The second problem i encountered was i forgot the local administrator password and security questions prior to joining the server to the domain. I managed to lock myself and wasn't able to recover the password.
There wasn't anything important on their but as i didn't take a snapshot i wasn't able to revert to a pervious working version and therefore had to rebuild it from scratch.

So what i learnt from this was once i got everything working i stored all passwords without a secure password manager on my local machine and created baseline snapshots of each VM.

#**ACTIVE DIRECTORY STRUCTURE**
I created a Main OU called:
CyberLab

Inside this i created a structure that looked professional even with it being a test lab.

These include:
CyberLab
├── Users
│   ├── IT
│   ├── Finance
│   └── HR
├── Workstations
├── Servers
├── Groups
└── Service Accounts

Once WIN11-01 was joined to the domain and was confirmed working i remoted on to SERVER01 and added the client Workstation into the Workstations OU.

I left SERVER01 in the default DC OU.

#**GROUPS**

I created three Global Security Groups:

GG_IT_Users
GG_Finance_Users
GG_HR_Users

I used GG to ensure I knew it was a Global Group

#**USERS**

I created a few test users so i can use them for testing later on.

I ensured domain users don't have any Domain admin rights but also created a backup service account and stored it in Service Accounts OU rather than in normal OU with other users.

What I learned

The biggest thing I noticed during this build was how important DNS is to Active Directory.

The Windows 11 machine could ping SERVER01, but that alone wasn't enough. I needed the client to use SERVER01 as its DNS server before the domain worked properly.

I also learned pretty quickly why snapshots are useful after locking myself out of the original Windows 11 VM.

Next

My next steps are:

Group Policy
Windows auditing
Sysmon
OPNsense
Wazuh
SOC investigation scenarios
Network monitoring

