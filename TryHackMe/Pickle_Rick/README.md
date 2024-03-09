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

Now, a good thing to do is enumerating the website to search for **hidden subdomains**. I will use Gobuster for that : `gobuster dir -u 10.10.124.198 -w directory-list-2.3-small.txt`.

I recommand that you use [Seclists](https://github.com/danielmiessler/SecLists) to brute-force with Gobuster. In this case, I will use Discovery/Web-Content/directory-list-2.3-small.txt.

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
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 297]
/.htpasswd            (Status: 403) [Size: 297]
/.hta                 (Status: 403) [Size: 292]
/assets               (Status: 301) [Size: 315] [--> http://10.10.124.198/assets/]
/index.html           (Status: 200) [Size: 1062]
/robots.txt           (Status: 200) [Size: 17]
/server-status        (Status: 403) [Size: 301]
Progress: 4723 / 4724 (99.98%)
===============================================================
Finished
===============================================================
```
One subdomain seem interesting : /robots.txt.

When we go on it, we find another nice clue.

<details>
    <summary>Clue 2</summary>
  
```
Wubbalubbadubdub
```

</details>

Maybe that could be enough for SSH now ?

### SSH (Port 22)
Let's try to connect with what we have : `ssh R1ckRul3s@10.10.124.198`.

```bash
ssh R1ckRul3s@10.10.124.198
The authenticity of host '10.10.124.198 (10.10.124.198)' can't be established.
ED25519 key fingerprint is SHA256:J71A6K+htxkobaUA0zeEtVVLlajw9L9dY7+1xi8cdII.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.124.198' (ED25519) to the list of known hosts.
R1ckRul3s@10.10.124.198: Permission denied (publickey).
```

Ok, nice try...












