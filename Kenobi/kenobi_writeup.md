# TryHackMe: Kenoby  

## [Task 1]Deploy the vulnerable machine

In this task I learned how to access Samba shares ,manupulate a vulnerable version of ProFTPd and gain initial access to escalate my priviledges to root via an suid binary

First connect to the machine and then run the command `nmap -p- -open -vv 10.10.111.60` to find all the open ports in this machine. From the image below you can see that there are 7 open ports with a service that is identified by nmap.
<img width="458" alt="1" src="https://user-images.githubusercontent.com/28839345/164231579-db35ad04-6e68-4c0c-9363-0880939a08ff.png">



**Scan the machine with nmap, how many ports are open?**

7



## [Task 2] Enumerating Samba for shares

Try to enumerate Samba shares by using the command `nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.111.60`.With this command you can check the number of shares that nmap has found  and use them later to login to the smb anonymously.
<img width="531" alt="2" src="https://user-images.githubusercontent.com/28839345/164231594-f66d8b90-fa61-42dd-97c7-a025b7283230.png">


- smb-enum-shares.nse: 
Attempts to list shares using the srvsvc.NetShareEnumAll MSRPC function and retrieve more information about them using srvsvc.
- smb-enum-users.nse: 
Attempts to enumerate the users on a remote Windows system, with as much information as possible, through two different techniques.The goal of this script is to discover all user accounts that exist on a remote system

Connect anonymously to the smb server by running the command `smbclient //10.10.111.60/anonymous`.

<img width="372" alt="3" src="https://user-images.githubusercontent.com/28839345/164231603-aa87ff69-e4a0-43ae-b609-10a0fd21c7b7.png">


Then run the command  `nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.111.60` to enumerate port 111.This port will lets you access a network file system.The output of the nmap enumeration is shown below.
<img width="566" alt="4" src="https://user-images.githubusercontent.com/28839345/164231606-dcd6eac4-3eb6-42e0-bddc-df7efdd876ac.png">

**Questions**

**Using the nmap command above, how many shares have been found?**

3

**Once you're connected, list the files on the share. What is the file can you see?**

log.txt

**What port is FTP running on?**

21

**What mount can we see?**

/var



## [Task 3] Gain initial access with ProFtpd

To get the version of the ProFtpd server use the command `nmap -p 21 -open -vv -sV  10.10.111.60` since ftp service runs in port 21.The output of this command is shown in the following image.

<img width="626" alt="Στιγμιότυπο 2022-04-20, 3 46 14 μμ" src="https://user-images.githubusercontent.com/28839345/164233566-2f9026fc-5008-4669-80e2-3c3ddf5c2a3b.png">

Since ProFTPd server version is 1.3.5 you can check for exploits through searchsploit by running the command `searchsploit ProFTPd 1.3.5`.

The exploit I used is the mod_copy module.

The mod_copy module implements SITE CPFR and SITE CPTO commands (analogous to RNFR and RNTO), which can be used to copy files/directories from one place to another on the server without having to transfer the data to the client and back.

Use netcat to connect to the ProFTPd server by running the command `nc 10.10.95.96 21` and then copy Kenobys private key to /var/tmp in the smb server by using the commmands `SITE CPFR` and `SITE CPTO`.Command `SITE CPFR` specifys the directory you are gonna copy from and command `SITE CPTO` specifys the directory you are gonna copy to.From the image bellow you can see that I copied the id_rsa from /home/kenobi to /var/tmp since I will then mount to /var/tmp to download the id_rsa file.
<img width="636" alt="7" src="https://user-images.githubusercontent.com/28839345/164231619-41690710-f948-4840-b388-09c78e8a78ba.png">

Mount /var/tmp directory to MACHINE_IP/var so you can later connect to Kenobi via ssh.




<img width="660" alt="8" src="https://user-images.githubusercontent.com/28839345/164231621-28449896-7396-444e-b1f6-0c9e455e3594.png">

After you mount to the server to /mnt/kenobiNFS copy the private ssh key(id_rsa) to your machine and connect to Kenoby via ssh.

<img width="529" alt="9" src="https://user-images.githubusercontent.com/28839345/164231628-2feaf1a5-2e8f-479c-972d-ebf8f3de07e4.png">


<img width="694" alt="10" src="https://user-images.githubusercontent.com/28839345/164231631-0ce0e893-f793-49b6-abe1-c7817a2b475a.png">


When you login to Kenobi via ssh go to /home/kenobi and run the command "cat user.txt" to get the flag.

**Questions**

**What is the version?**

1.3.5

**How many exploits are there for the ProFTPd running?**

4



## [Task 4]Privilege Escalation with Path Variable Manipulation

In this task you are gonna learn how to use suid binaries and path variable to escalate your priviledges to root.

Run the command `find / -perm -u=s -type f 2>/dev/null` to search the system for binaries with the suid bit set.
<img width="509" alt="11" src="https://user-images.githubusercontent.com/28839345/164231635-27f3ecc1-82bc-469c-9841-399304f41f32.png">



Type the command `/usr/bin/menu` and check the menu that is getting displayed

<img width="370" alt="12" src="https://user-images.githubusercontent.com/28839345/164231637-311d689e-5321-43bd-81a4-ef5396481ea1.png">


Use the `string` command to see the actual commands that are being executed when you pick a choise from the menu.
<img width="359" alt="13" src="https://user-images.githubusercontent.com/28839345/164231642-65fb84e1-941d-47a4-bc76-d8bb27681c55.png">





Go to tmp directory since every user can write in this directory and echo `/bin/sh` to `curl`.Make curl executable and change the Path variable.The reason you should export the PATH as shown in the below picture is so when you run the command curl it is first gonna check to the /tmp directory to find if there is an executable binary for curl and if it is then it will execute `/bin/sh`.By running `/bin/sh` then you will immediately spawn a shell as root and escalate your priviledges.

<img width="424" alt="14" src="https://user-images.githubusercontent.com/28839345/164231644-7a1e4f1c-51dd-40a4-b628-88c6f861c0cf.png">



Finally run the`id` command and check if uid=0(root).


<img width="968" alt="15" src="https://user-images.githubusercontent.com/28839345/164234561-2a9c72bb-d432-4fb5-bd01-d2d1b7c7c221.png">

**Questions**

**What file looks particularly out of the ordinary?**

`/usr/bin/menu`

**Run the binary, how many options appear?**

3
