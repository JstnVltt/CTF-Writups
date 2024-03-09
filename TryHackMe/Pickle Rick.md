# Pickle Rick
#### Author : [ar33zy](https://tryhackme.com/p/ar33zy)
#### Date : 09/03/2024
#### Link : https://tryhackme.com/room/picklerick
Note : the IP of the machine could be different for you !

## Challenge
### Scan 
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









