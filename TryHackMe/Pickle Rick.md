# Pickle Rick
#### Author : [ar33zy](https://tryhackme.com/p/ar33zy)
#### Date : 09/03/2024
#### Link : https://tryhackme.com/room/picklerick
Note : the IP of the machine could be different for you !

## Challenge
First, let's perform a scan of the machine with `nmap -vv -T 5 -sV -sC -oN nmap.txt 10.10.124.198` to see if any port is open.

- `-vv` : Having minimum verbose mode
- `-T 5` : Scan with full speed
- `-sV`: Attempts to determine the version of the service running on port
- `-sC` : Scan with default NSE scripts. Considered useful for discovery and safe
- `-oN` : Normal output to the file nmap.txt
- `-Pn` : Treat all hosts as online



