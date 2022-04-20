# TryHackMe: Vulversity

In this room I learned some basic things about recon,web app attacks and priviledge escalation.

## Task 1 [Deploy the machine]

In the first task you need to connect and deploy the machine

## Task 2 [Reconnaisance]

This task is all about learning basic stuff about recon using nmap. Run the command "nmap -sV -A 10.10.205.97" to check the open port numbers, the version of the squid proxy, the operating system of this machine and the port this web server is running.

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqQ_ofysO5rMOJhX1ZSVrhP9sCTqM1mlohQx4A6lHqCXrm9QNYJD6hD4If7YvpKfp-00v4uoFcj33_o-NJjLCk9-QIfUm57MKvj4jk2Nd2-10Uq11x4m_hpBKaX-S90DTDaEqAb5HqVCS1XVZwdgF8jx60rWjyNVInH8OvXYq5LnHBU2h9fUqP9T8mUg/w431-h365/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-18,%2012.08.17%20%CF%80%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjsoa_1m7k32IwZ6QoAqqJXfbE5Tt1EPMH7GyPMQwV6eQoBoRo-BBB03pKbSgmhTJrDKV39WuCJOPIlfuDN2ayAs59UZLnZZJHT_EZzxveTkbKYlPLCLTWllzNfcDKRM615NtAaqUo1YenRBRzDQ4Cg7yeZ3htZy4YcrRm6KMdchaHGbxnEh6w1BJIRqQ/w431-h343/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-18,%2012.08.35%20%CF%80%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

**Questions**
  
**Scan the box, how many ports are open?**

6

**What version of the squid proxy is running on the machine?**

3.5.12

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZtytdfFL5xZYkQW4P8g2w0_UlGczWMeBY4CwShJ3Uh4h6MhRXsCh1RcTvIEZuMW_u691pRXXbFgJlELSK_SoLd7mHX-9x2bgKA6s7VbDcXgYmlgCsaWwBomoqU6NKk03ip52EQmLasxWFpT1rpC7IlaQRxbJi1aiixapnxAI3xRlDvxlLcF4ImsYv7g/w600-h28/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%208.32.35%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

**How many ports will nmap scan if the flag  -p-400 was used?**

400

**Using the nmap flag -n what will it not resolve?**

DNS

**What is the most likely operating system this machine is running?**

Ubuntu

**What port is the web server running on?**

3333

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIsiotPNXVXsKaKAq2eyRcDMshrwAX44Upy5K5PFUZjTL3uEcVVhtkzJc9Ogf0CjaOeFKiIuXiYVGRvGhyXndgmMk5XIyEACAG9c1QRjBwp6FpVD_A80ErYJ3XA_5uSm6Dk4OKjsN-on4v129E33nicHNPnnjlU3Gw1_xggZQM5UJMFhvG185_q55VKQ/w320-h12/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%208.43.46%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

## Task 3[Locating directories using GoBuster]

  

This task is all about learning the basic usage of gobuster which is a tool used to brute-force URIs.Run the command "gobuster dir -u http:10.10.205.97:3333 -w /usr/share/wordlists/dirb/common.txt" to find the directory that has an upload form page.

  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEijf61toH2gVQCtNBl8JE9kvvCTu9O0G1SRGtAr_wG78o61aiNWkMAF5CULmrIXfiNsTNLBbDNHsOFF4fLFE7RRM7m5otmfFR3dMqw_yDIo1WGI3R1VZ71tyrky3Arqq3j3jHngBh89wvRtplsiQezfFoK-prD4gKuMGixvGLyOA2O64BMLLOjK0_mUVw/w387-h304/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%208.52.14%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

## [Task 4] Compromise the webserver

  

To answer the first question of this task I tried to upload a php file in the form and got a message 'Extension not allowed'.

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjf6exOlgOsCSDFnL4YEDPI3Od1DOQwPjDnbgdEAGOWTF4TADWp6mWv7gdVQ-OowH5cK5KSaZman9tVl1-BFBQTGOtKwZKE0_IbZV0o_aZz7LiXUAXe4ObEzS8vxyJG-aQb2WIqZXpeTkkJ_orf0nWseAcSiV6GyqiW_teUkkh-19gtcDSNMmfETG5ydg/w357-h76/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2010.57.42%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

After this I discovered that the files with .php extension were being blocked so I used burp suite to find what extension was not being filtered.After I created the list of .php,.php3,.php4,.php5 and .phtml files ,I used this file in combination with intruder to check what extension was not filtered.

  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOphcUYDu-TkVKnpwwyur5tTEEnCgXNEbILRHi5LtdQRBNuCgpOHJmx1iy18KSIQfVxUBkjG-T9ROg7IQohfoCYRt2Qjr8oYWvgAmr0clOY0Am6ZCg2WrRsFCbPCIM53nuEA7mARL9kMcBuBYkBaxqRYqUJ89PwQte9LiToJuqTkvXHB9vjdc7woQh5w/w635-h285/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.00.14%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghiBnxeXh173zrrTB0ojAfRVBmh3aU4Lir0QCfZUGDnhgC_nJdIl3euO9uxV2BD6n6hRJJpgnpILdyHo0X3g3ThEbupmxV865hH3sPmlcWkkFQJOhpRunBa8U24mtHewmg5TZhYbmSMe_tEybs_TNhvntrrf4Cp0yjt-N1cWDSVHvS2_XGCUCJXmds6A/w825-h23/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.00.49%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

  

The output of the sniper attack was the following 

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimPg-97LG8k_zlK3GSl-g8u4UDPy7tuxEwLBpcdTxDqjOD4O1G2l-dZ_G2lcAAyVeiSO0_mIpGmvUt9KV4eqQWiz820BpBfv4zK3TUwXmiQsQIHLRk65CQmHvwlRxHKGAOWk3LWqbhcX_9KTPRIrmvM4x-DLHYXOQ9XHnHYTL76CZtHF_VLYY3XtV81g/w735-h147/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.01.52%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

  

  

  

  

As we can see there is no difference beetween different php extensions on the Length so I tried manually using burp to change the file extension.

After changing the filename parameter to "file.phtml" I found that the php extension that is not filtered is the ".phtml".

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjScZEfGazC8sVi3pFLzyPbjYdUgrS44iA6AAqdMVHKNNoZqC9_0IxVshFCe9Si1MrFrZd3gWDgbK09MDS-X0N_-5RaVwdIu3r-h7DgqcWJtFF0S952j4LvmpYir2bD92Tp81s0ydAPmowsKBT99TVY160VNZsyc5rTB6SLH7v4c5xTmYz6o5llz-jgA/w1077-h37/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.03.30%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

Then I used the script mentioned in tryhackme to create reverse_shell.I changed the ip parameter to local host ip and port number to 4444 and uploaded the file on the site.Then I run netcat on my local machine "nc -nlvp 4444" and clicked on the uploaded php file on the web server to create a reverse backdoor.

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiPH_gA81DthLV10X765-wbh3f6U8RksUMII6_ZIRlNBkwgxph2JsccJCg7tntAx4rAv-uDWYdsTO37ADE2hvy6HzUf4TycwpauAo59Z5d6PahVsNFvQiFp7Zilwz9yEqBG4kY70oEbSkAd66fit0DFsun8JLbushqJYF_YZn-T9pMNAoMtBq8GDkeBlA/s320/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.07.04%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSS4vZkoIulRqZNc9CDkcAVhTDKa7Y1ivSOyTlBDpWPRjp55WrmouRnr84YOf_7RsbT-q2K7-Ohb2cin8y3emOSxPWVOJlM1i0ozsibofLRhcMN0p1qDJfahWVv1qYva-F1dqQgxqGDLNTR2VcQNi_NAf3mJU_GutadjeXjXuNylEs92HZJPtOfYqMaQ/s320/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.08.17%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

Then after going in the home directory I found a directory called bill which is the user that manages the web server

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1zcXTy-Fx8KKFkIT3G-rz8fBQB1cOxcaR1o6wg_QuKEatBe45CpQEwTd28ZgQ-vBrK3ok41_JbAMb-cPRp7b6VEHtUTphMYvMsZxVqzyQJjf0eV27467YOPQQx4fIk2KCzW5ZkzcVKM8OrAeZhslsDKta1FKLSLlNRIIIYgW7r5eiYO_BI6gcmBQ0IQ/s320/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.09.23%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

Finally in the bill directory was the user.txt file which contained the flag for Task 4.

**Questions**

**What is the name of the user who manages the webserver?**

bill

  

**[Task 5] Priviledge Escalation**

In the last task after I used the command "find / -perm -u=s -type f 2>/dev/null" I was able to find all the files with the suid bits set.

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjaSMu8_tIHGJ0uLlyg3Co5F1sQ-QQJsKr7ukZ7vHABTWnFsZtRGjlwI8I6FXeYvotHqSlpojrbLG7BJ1XwUFRcRPD5tyssetgOY59xDs0Lp2ABUqZrc6Xx2_zChhQqqaSd0jBSbL0rlmRez6NE_6Wd-VneiAXZ5fM26eS8kbXVymiYTyX51vg0S45pbQ/s320/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.18.44%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

  

  

After searching those files in GTFO bins I found that if systemctl has the suid bit set it does not drop the elevated privileges and may be abused to access the file system, escalate or maintain privileged access as a SUID backdoor.

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQuUjCuv1yK7qoA0Iz4UJL1WZyw5NiEazRCwjVvdHm75w4yiCE8k3Mi5B_aM-WWwDeQyQ2ygaC9cxO67Jh5rlrwT48B7y8yOIpmolLkPIdmYYoIJSyDIS3lMfTa_H7OAgU-7jKUSi1Cc8xQetGcygf9kTWR47sgp0zaxkbzjM_Tb9zDHb4_9tef3HUkQ/s320/%CE%A3%CF%84%CE%B9%CE%B3%CE%BC%CE%B9%CE%BF%CC%81%CF%84%CF%85%CF%80%CE%BF%202022-04-17,%2011.48.35%20%CE%BC%CE%BC.png)](https://www.blogger.com/blog/page/edit/6040287426171712342/4409551937287025563#)

  

  

  

The I run the above script the above script and after this the command "bash -p" to become root.

The final step to get the flag is run the commanc "cat /root/root.txt" to get the flags needed to complete this task

**Questions**

**On the system,search for all SUID files. What file stands out?**

/bin/systemctl
