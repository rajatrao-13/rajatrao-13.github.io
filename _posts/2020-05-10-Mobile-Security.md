---
layout: post
title: Mobile Security
author: Rajat Rao
date: '2020-06-10 14:35:23 +0530'
category:
        - HackTheBox
summary: Beginners guide to mobile security (Android).
thumbnail: /assets/img/posts/mobile_security.png
---

<style>
* {
  box-sizing: border-box;
}

.column {
  float: left;
  width: 33.33%;
  padding: 5px;
}

/* Clearfix (clear floats) */
.row::after {
  content: "";
  clear: both;
  display: table;
}
</style>

As security audit of web application are performed for multiple organization, a draw of interest can be made in Mobile applications as well.

The primary objective for a mobile application penetration test is to identify exploitable vulnerabilities in code, system, network, application, databases, Api's etc before hackers are able to discover and exploit them. Application penetration testing will reveal real-world opportunities for hackers to be able to compromise application and databases in such a way that allows for unauthorized access to sensitive data or even take-over application for malicious/non-business purposes.

Android Penetration testing can be implemented in any organization:

`1)Static:` Checks with respect to code levels will be performed

`2)Network:` Intercepting the request sent by the android application and interaction between the application and server is audited.

`3)Interactive:` While the applications provide its service, they tend to store a lot of confidential data in the respective mobile device itselfdynamically. An audit of the internal storage of the device is performed.
	
	
The Mobile Security Series will be divided into the following chapters:


--> <a href="#fundamentals">Chapter 1: Android Fundamentals (Basics)</a>

--> <a href="">Chapter 2: Static Analysis</a>

--> <a href="">Chapter 3: Dynamic Analysis</a>

--> <a href="">Chapter 4: Interactive Analysis</a>

&nbsp;

<h2>Chapter 2: Setup Mobile Testing Platform  </h2>

Setting up an environment for any security assessment is a must. For Android Security, it is suggested to be dependent more on Kali Linux/Tamer operating system. <b>Kali Linux</b> is an advanced penetration testing Linux distribution used for Penetration Testing, Ethical Hacking and network security assessments.<b>Android Tamer</b> is a virtual/live platform for Android Security professionals where all the tools are inculcated within the OS. 

The very first step is to install Virtual Kali Linux/Android Tamer on Vmware. Following is the list of useful URLs for downloads:

<b>VMware</b>: [https://www.vmware.com/in/products/workstation-player/workstation-player-evaluation.html](https://www.vmware.com/in/products/workstation-player/workstation-player-evaluation.html)

<b>Kali Linux</b>: [https://www.kali.org/downloads/](https://www.kali.org/downloads/)

<b>Android Tamer</b>: [https://androidtamer.com/tamer4-release](https://androidtamer.com/tamer4-release)

For newbies into cyber security, we'll be creating content for installation steps for <b>VMWare</b> and <b>virtual machines</b> (.iso and .ova)

For assessing Mobile application, we need a dedicated android testing device to perform assessments. However, the same can be performed by creating a virtual Android environment with the help of a tool named Genymotion. It is a Cross-platform Android Emulator for developers & QA engineers. It is dependent on VirtualBox. Following is the list of URL for downloads-

<b>VirtualBox</b>: [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

<b>Genymotion</b>: [https://www.genymotion.com/fun-zone/](https://www.genymotion.com/fun-zone/)    
     
&nbsp;
      
<h3>2.1 || Genymotion</h3>

--> `Start Genymotion`

![deploy using travis](/assets/img/posts/Mobile_Security/1.png){:class="img-fluid"}

--> `Add Virtual Device`

![deploy using travis](/assets/img/posts/Mobile_Security/2.png){:class="img-fluid"}

--> `Select Google Pixel XL with API >=6.0 and click Next`

![deploy using travis](/assets/img/posts/Mobile_Security/3.png){:class="img-fluid"}

--> `Install the selected emulator`

![deploy using travis](/assets/img/posts/Mobile_Security/4.png){:class="img-fluid"}


![deploy using travis](/assets/img/posts/Mobile_Security/5.png){:class="img-fluid"}  

&nbsp;

<h3>2.2 || Required tools</h3>

Once genymotion is installed, the next step for android security assessments is to setup following list of tools in <b>Kali Linux</b>:

<b>Mobsf (Docker)</b>: [https://github.com/MobSF/Mobile-Security-Framework-MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF) 

<b>Drozer</b>: [https://github.com/FSecureLABS/drozer/releases](https://github.com/FSecureLABS/drozer/releases) 

<b>Superanalyzer</b>: [https://github.com/FSecureLABS/drozer/releases](https://github.com/FSecureLABS/drozer/releases)  
  
  &nbsp; 


<h2>Chapter 3: Static Analysis  </h2>

Static Application Security Testing is a set of technologies designed to analyze the application source code that are indicative of security vulnerabilities. Now as required tools are installed, we can start with security assessment of Android Applications/

<h3>3.1 || Mobsf (Docker)</h3>

<b>Mobile Security Framework (MobSF)</b> is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware-analysis and security assessment framework capable of performing static and dynamic analysis.

In this section of <b>Mobsf</b>, we’ll be focusing more on static analysis.

--> `Start mobsf via Docker`

```yml
$ git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
$ cd Mobile-Security-Framework-MobSF
$ docker build -t mobsf .
$ docker run -it -p 8000:8000 mobsf
```
--> `Upload and Scan APK`

![deploy using travis](/assets/img/posts/Mobile_Security/6.png){:class="img-fluid"}  

![deploy using travis](/assets/img/posts/Mobile_Security/7.png){:class="img-fluid"}  

  &nbsp; 
  
<h3>3.2 || Superanalyzer </h3>

--> `Run Superanalyzer against APK`

```yml
$ super-analyzer -v goatdroid.apk
```
The output of the super-anaylzer is stored in the form of HTML and can view defects found with respect to the application.

![deploy using travis](/assets/img/posts/Mobile_Security/8.png){:class="img-fluid"}  

  &nbsp; 
  
<h3>3.3 || d2j-dex2jar & jd-gui </h3>
	
<b>d2j-dex2jar</b> tool helps one to reverse engineer the apk to jar version of it. The jar version helps to displays the code and can be examined for security purposes.

```yml
$d2j-dex2jar goatdroid.apk
$jd-gui → select jar file of goatdroid apk
```
![deploy using travis](/assets/img/posts/Mobile_Security/9.png){:class="img-fluid"}  

  &nbsp; 

<h2>Chapter 4: Network Analysis </h2>

Network analysis deals with communication of application with the server providing data. Here, analysis is done similarly the way analysis of web application is done by setting a proxy and intercepting every request to <b>Burpsuite</b>.

<h3>4.1 || Setup Proxy in Mobile </h3>

<div class="row">
  <div class="column">
    <img src="/assets/img/posts/Mobile_Security/10.png" alt="Snow" style="width:100%">
  </div>
  <div class="column">
    <img src="/assets/img/posts/Mobile_Security/11.png" alt="Forest" style="width:100%">
  </div>
  <div class="column">
    <img src="/assets/img/posts/Mobile_Security/12.png" alt="Mountains" style="width:100%">
  </div>
</div> 

