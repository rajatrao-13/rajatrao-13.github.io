---
layout: post
author: Rajat Rao
title: HackTheBox Monteverde Writeup
date: 2020-06-05T09:52:20.613Z
thumbnail: /assets/img/posts/HTB/Monteverde/card.png
category: HackTheBox
summary: HackTheBox Monteverde Writeup
---

## Brief
&nbsp; &nbsp; On enumerating different services of `Monteverde` machine, we were able extract list of users available in the target machine. Due to security misconfiguration, one of the users has set the password same as the username and this was identified by brute forcing the same using `smb_login` module from `metasploit`. Levering the misconfiguration, we were able to login to `SMB` with the help of `smbclient` and on further enumeration, we were able to extract a file named `azure.xml` under `mhope` user folder consisting of credentails of the same user. Making the use of credential found, we were able to get `Windows Remote Management` shell with the help of tool named `evil-winrm` which targets on port `5985` port. Here, we were able to get basic shell and were able pwn the `user.txt` part of the challenge. Moving to privilege escalation part, enumemrating local system, we were able to understand the target machine has `Azure Active Directory Connect`. On searching for exploits on google, we were able to find a script and privilege to highest level of authorization and complete the final challenge of getting `root.txt`

&nbsp;

## Flow
- `enum4linux` reveals list of users name

- Brute Forcing `SMb` via `metasploit` using the usernames as password identifies a legit credential (`SABatchJobs:SABatchJobs`)

- Login as `SABatchJobs` in `SMB` via `smbcleint`

- Enumerate `mhope` folder and download `azure.xml` which consists of mhope credentials (`mhope:4n0therD4y@n0th3r$`)

- Login as `mhope` in Win-RM Service (5985) using `evil-winrm`

- Get `user.txt`

- Local enumeramation discloses usage of `Azure AD Connect` in target machine

- Search for Azure AD exploit leads to powershell script (module) to generate new `administrator` password in the target machine

- Use newly generated `administrator` credentials and login as `administrator` in `Win-RM service` (5985) using `evil-winrm`

- Get `root.txt`

&nbsp;

Below is a detail writeup of the machine following the below steps:

1. [Information Gathering]({{site.url}}/hackthebox/2020/06/05/HackTheBox-Monteverde/#informationgathering)

2. [Low-Privilege Shell]({{site.url}}/hackthebox/2020/06/05/HackTheBox-Monteverde/#lowprivilegeshell)

3. [Privilege Escalation]({{site.url}}/hackthebox/2020/06/05/HackTheBox-Monteverde/#privilegeescaltion)

<p id="informationgathering">&nbsp;</p>

## 1. Information Gathering 
&nbsp;
### 1.1 || Nmap 

-> `Run nmap`
```yml
$ nmap -sC -sV -p- -oA full 10.10.10.172
```
where, 

&nbsp; &nbsp; -sC : Run Default Scripts

&nbsp; &nbsp; -sV : Enumerate different versions

&nbsp; &nbsp; -sC : Store the output in all format (.nmap, .xml and .gnmap) having name full

**Result:**

```yml

# Nmap 7.80 scan initiated Tue May 26 14:02:41 2020 as: nmap -sC -sV -p- -oA full -Pn 10.10.10.172
Nmap scan report for 10.10.10.172
Host is up (0.13s latency).
Not shown: 65516 filtered ports
PORT      STATE SERVICE       VERSION
53/tcp    open  domain?
   fingerprint-strings: 
   DNSVersionBindReqTCP: 
     version
_    bind
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2020-05-26 07:53:02Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
 _http-server-header: Microsoft-HTTPAPI/2.0
 _http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49667/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  msrpc         Microsoft Windows RPC
49706/tcp open  msrpc         Microsoft Windows RPC
49778/tcp open  msrpc         Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.80%I=7%D=5/26%Time=5ECCD5D6%P=x86_64-pc-linux-gnu%r(DNSV
SF:ersionBindReqTCP,20,"\0\x1e\0\x06\x81\x04\0\x01\0\0\0\0\0\0\x07version\
SF:x04bind\0\0\x10\0\x03");
Service Info: Host: MONTEVERDE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
 _clock-skew: -46m43s
  smb2-security-mode: 
    2.02: 
 _    Message signing enabled and required
  smb2-time: 
    date: 2020-05-26T07:55:26
 _  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue May 26 14:14:49 2020 -- 1 IP address (1 host up) scanned in 728.28 seconds

```
&nbsp;

### 1.2 || enum4linux

**Enum4linux** is a tool for enumerating information from Windows and Samba systems. It attempts to offer similar functionality to enum.exe formerly available from www.bindview.com. 
&nbsp;

-> `Run enum4linux`

```yml
enum4linux 10.10.10.172
```

**Result:**
```yml
enum4linux 10.10.10.172
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue May 26 13:41:48 2020

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 10.10.10.172
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 10.10.10.172    |
 ==================================================== 
[E] Can't find workgroup/domain


 ============================================ 
|    Nbtstat Information for 10.10.10.172    |
 ============================================ 
Looking up status of 10.10.10.172
No reply from 10.10.10.172

 ===================================== 
|    Session Check on 10.10.10.172    |
 ===================================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 437.
[+] Server 10.10.10.172 allows sessions using username '', password ''
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 451.
[+] Got domain/workgroup name: 

 =========================================== 
|    Getting domain SID for 10.10.10.172    |
 =========================================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 359.
Domain Name: MEGABANK
Domain Sid: S-1-5-21-391775091-850290835-3566037492
[+] Host is part of a domain (not a workgroup)

 ====================================== 
|    OS information on 10.10.10.172    |
 ====================================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 458.
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 10.10.10.172 from smbclient: 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 467.
[+] Got OS info for 10.10.10.172 from srvinfo:
Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED

 ============================= 
|    Users on 10.10.10.172    |
 ============================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 866.
index: 0xfb6 RID: 0x450 acb: 0x00000210 Account: AAD_987d7f2f57d2	Name: AAD_987d7f2f57d2	Desc: Service account for the Synchronization Service with installation identifier 05c97990-7587-4a3d-b312-309adfc172d9 running on computer MONTEVERDE.
index: 0xfd0 RID: 0xa35 acb: 0x00000210 Account: dgalanos	Name: Dimitris Galanos	Desc: (null)
index: 0xedb RID: 0x1f5 acb: 0x00000215 Account: Guest	Name: (null)	Desc: Built-in account for guest access to the computer/domain
index: 0xfc3 RID: 0x641 acb: 0x00000210 Account: mhope	Name: Mike Hope	Desc: (null)
index: 0xfd1 RID: 0xa36 acb: 0x00000210 Account: roleary	Name: Ray O'Leary	Desc: (null)
index: 0xfc5 RID: 0xa2a acb: 0x00000210 Account: SABatchJobs	Name: SABatchJobs	Desc: (null)
index: 0xfd2 RID: 0xa37 acb: 0x00000210 Account: smorgan	Name: Sally MorganDesc: (null)
index: 0xfc6 RID: 0xa2b acb: 0x00000210 Account: svc-ata	Name: svc-ata	Desc: (null)
index: 0xfc7 RID: 0xa2c acb: 0x00000210 Account: svc-bexec	Name: svc-bexec	Desc: (null)
index: 0xfc8 RID: 0xa2d acb: 0x00000210 Account: svc-netapp	Name: svc-netapp	Desc: (null)

Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 881.
user:[Guest] rid:[0x1f5]
user:[AAD_987d7f2f57d2] rid:[0x450]
user:[mhope] rid:[0x641]
user:[SABatchJobs] rid:[0xa2a]
user:[svc-ata] rid:[0xa2b]
user:[svc-bexec] rid:[0xa2c]
user:[svc-netapp] rid:[0xa2d]
user:[dgalanos] rid:[0xa35]
user:[roleary] rid:[0xa36]
user:[smorgan] rid:[0xa37]

 ========================================= 
|    Share Enumeration on 10.10.10.172    |
 ========================================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 640.

	Sharename       Type      Comment
	---------       ----      -------
SMB1 disabled -- no workgroup available

[+] Attempting to map shares on 10.10.10.172

 ==================================================== 
|    Password Policy Information for 10.10.10.172    |
 ==================================================== 
[E] Unexpected error from polenum:


[+] Attaching to 10.10.10.172 using a NULL share

[+] Trying protocol 139/SMB...

	[!] Protocol failed: Cannot request session (Called Name:10.10.10.172)

[+] Trying protocol 445/SMB...

	[!] Protocol failed: Missing required parameter 'digestmod'.

Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 501.

[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 7


 ============================== 
|    Groups on 10.10.10.172    |
 ============================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 542.

[+] Getting builtin groups:

[+] Getting builtin group memberships:
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 542.

[+] Getting local groups:
group:[Cert Publishers] rid:[0x205]
group:[RAS and IAS Servers] rid:[0x229]
group:[Allowed RODC Password Replication Group] rid:[0x23b]
group:[Denied RODC Password Replication Group] rid:[0x23c]
group:[DnsAdmins] rid:[0x44d]
group:[SQLServer2005SQLBrowserUser$MONTEVERDE] rid:[0x44f]
group:[ADSyncAdmins] rid:[0x451]
group:[ADSyncOperators] rid:[0x452]
group:[ADSyncBrowse] rid:[0x453]
group:[ADSyncPasswordSet] rid:[0x454]

[+] Getting local group memberships:
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Group 'Denied RODC Password Replication Group' (RID: 572) has member: Couldn't lookup SIDs
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Group 'ADSyncAdmins' (RID: 1105) has member: Couldn't lookup SIDs
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 574.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 593.

[+] Getting domain groups:
group:[Enterprise Read-only Domain Controllers] rid:[0x1f2]
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[DnsUpdateProxy] rid:[0x44e]
group:[Azure Admins] rid:[0xa29]
group:[File Server Admins] rid:[0xa2e]
group:[Call Recording Admins] rid:[0xa2f]
group:[Reception] rid:[0xa30]
group:[Operations] rid:[0xa31]
group:[Trading] rid:[0xa32]
group:[HelpDesk] rid:[0xa33]
group:[Developers] rid:[0xa34]

[+] Getting domain group memberships:
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'Domain Guests' (RID: 514) has member: MEGABANK\Guest
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'Trading' (RID: 2610) has member: MEGABANK\dgalanos
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'HelpDesk' (RID: 2611) has member: MEGABANK\roleary
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'Azure Admins' (RID: 2601) has member: MEGABANK\Administrator
Group 'Azure Admins' (RID: 2601) has member: MEGABANK\AAD_987d7f2f57d2
Group 'Azure Admins' (RID: 2601) has member: MEGABANK\mhope
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'Domain Users' (RID: 513) has member: MEGABANK\Administrator
Group 'Domain Users' (RID: 513) has member: MEGABANK\krbtgt
Group 'Domain Users' (RID: 513) has member: MEGABANK\AAD_987d7f2f57d2
Group 'Domain Users' (RID: 513) has member: MEGABANK\mhope
Group 'Domain Users' (RID: 513) has member: MEGABANK\SABatchJobs
Group 'Domain Users' (RID: 513) has member: MEGABANK\svc-ata
Group 'Domain Users' (RID: 513) has member: MEGABANK\svc-bexec
Group 'Domain Users' (RID: 513) has member: MEGABANK\svc-netapp
Group 'Domain Users' (RID: 513) has member: MEGABANK\dgalanos
Group 'Domain Users' (RID: 513) has member: MEGABANK\roleary
Group 'Domain Users' (RID: 513) has member: MEGABANK\smorgan
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'Group Policy Creator Owners' (RID: 520) has member: MEGABANK\Administrator
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 614.
Group 'Operations' (RID: 2609) has member: MEGABANK\smorgan

 ======================================================================= 
|    Users on 10.10.10.172 via RID cycling (RIDS: 500-550,1000-1050)    |
 ======================================================================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 710.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 710.
[E] Couldn't get SID: NT_STATUS_ACCESS_DENIED.  RID cycling not possible.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 742.

 ============================================= 
|    Getting printer info for 10.10.10.172    |
 ============================================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 991.
Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED


enum4linux complete on Tue May 26 13:46:08 2020

```

On examining the above output, we were able to identify usernames being revealed in the output. To get the list of users, we issued the following command:

```yml
cat enum4linux | grep MEGABANK | cut -d ":" -f3 | cut -d '\' -f2 > user_list.txt
```
**Result**
```yml
Guest
dgalanos
roleary
Administrator
AAD_987d7f2f57d2
mhope
Administrator
krbtgt
AAD_987d7f2f57d2
mhope
SABatchJobs
svc-ata
svc-bexec
svc-netapp
dgalanos
roleary
smorgan
Administrator
smorgan
```
&nbsp;

### 1.3 || SMB Brute Force

As the list of usernames was extracted, we'll be trying to brute force the SMB service using ***metasploit***. Using `auxiliary/scanner/smb/smb_login` in metasploit and setting the options as shown in below screenshot:

![deploy using travis](/assets/img/posts/HTB/Monteverde/1.png){:class="img-fluid"}  

![deploy using travis](/assets/img/posts/HTB/Monteverde/2.png){:class="img-fluid"}  

&nbsp;

### 1.4 || SMB Enumeration

On SMB Brute forcing to the target macine, we were successfully able to get a legit set of credentials. To enumrate SMB, we'll be using [smbclient]()

asd
