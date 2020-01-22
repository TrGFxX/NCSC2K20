# Securinets Developers Team ( USER + ROOT ) #
# 1000 points , 2 solves #


![TASK](https://imgur.com/GuOqjDE.png)

Hello , in this task we have a target IP address that we need to exploit to get user & root flag. So lets start ! 

# USER FLAG # 

First of all , we start by doing an nmap scan on the given IP:

![NMAP](https://imgur.com/ggsh7Y8.png)

We can see that we have port 80 open, checking the IP address in the browser gives us : 

![WEB](https://imgur.com/rM572cW.png)

Hmm , nothing really useful here , so lets search for other directories in the website : 

![DIRB](https://i.imgur.com/LGzEiN6.png)

Result of Directory Enumeration gave us /develop , but the directory is forbidden :

![WEB](https://imgur.com/4gQqiWe.png)

So we didnt find anything useful on port 80 , back to the nmap scan results , we can see that we have SMB protocol port open.

SMB is a client-server communication protocol used for sharing access to files, printers, serial ports and other resources on a network.

So lets enumerate the SMB:

![SMB](https://imgur.com/Yt8hWoV.png)

the smbclient is a tool that is used to interact with SMB Server , we use the -L option to list the shares available.
Like you see , the server asks for a password to proceed but we can check for anonymous access by hitting enter without providing a password 

![SMB](https://imgur.com/4nAYqTY.png)

And we are in ! You can see that there's a share named Developement that belongs to Securinets Developpers. Interesting ! 
So let's try to access to that share with the anonymous access like we did in the previous step :

![SMB](https://imgur.com/Cx2DJzV.png)

BOOM! We are in the share without supplying a password ! lets try to list files/directories :

![SMB](https://imgur.com/MMTB7w9.png)

The share is empty. If we think about the relation between the smb share found and the forbidden directory that we found on the port 80 , maybe this share points to that directory ..
Lets try to upload a file on the share ! 

![SMB](https://imgur.com/jk2YxRV.png)

Upload successful !! Now back to the forbidden directory and lets try to access to our uploaded file !

![WEB](https://imgur.com/ge7O8t4.png) 

Indeed ! we got our uploaded file ! So the coming steps are obvious now , we need to upload php reverse shell to get a reverse shell on the machine.
For this , we need a VPS.

After uploading the php reverse shell and setting up the listener on our machine :

![REV](https://imgur.com/kbUYLHt.png)

Nice ! lets upgrade our reverse shell to a tty shell for more stability ! 

![REV](https://imgur.com/uxD0GI9.png)

As we can see , we are on a low privilege shell (www-data) ! so we need to escalate privileges .

Lets enumerate the machine by executing the famous LinEnum script.

![ENUM](https://imgur.com/omWM6Ow.png)
Interesting result !
![ED](https://imgur.com/3mzUWxL.png)

So www-data can execute the editor ed as user1 without password ! The idea then is to spawn a shell via the ed editor by supplying !/bin/bash

![USER1](https://imgur.com/Pqc85rS.png)

BINGO ! We have escalated our privileges to user1 now ! the user flag is in the home directory of user1 : 

![FLAG](https://imgur.com/lKRGKXU.png)


# ROOT FLAG #

After enumerating again the machine as user1 , we can see that there's an unusual suid binary owned by root: 

![ROOT](https://imgur.com/tD9Oo2y.png)

If an executable file on Linux has the “suid” bit set when a user executes a file it will execute with the owners permission level and not the executors permission level. Meaning if you find a file with this bit set, which is owned by a user with a higher privilege level than yourself you may be able to steal their permissions set.

So we can exploit this suid binary and read the root flag that is located in /root/root.txt

xxd is a Linux command that creates a hex dump of a given file or standard input.

So the trick here that we can use to read the flag is to get the hex dump of the root flag and reverting it by piping it again to the xxd command with the revert option -r

![ROOT](https://imgur.com/s8a0OB6.png)


BINGO.



