# VulnHub Write-Up - Empire: Breakout

## 1. Basic Information

**Platform: VulnHub**

**Machine name: Empire: Breakout**

**Difficulty level: Easy**

## 2. Reconnaissance

First, i runned a nmap scan to identify open ports, running services, and their versions.

**Command:** nmap -sV -sC -p- -n -T5 TARGET-IP

**Results:** **80/tcp open http, 139/tcp open netbios-ssn, 445/tcp open netbios-ssn, 10000/tcp open http, 20000/tcp open http**

<img width="690" height="291" alt="{8095E2B5-94D4-4F7F-840C-52C32C553CFA}" src="https://github.com/user-attachments/assets/d8b95c30-7d73-4a8d-9a51-46c41e1442ee" />

Under 80 port is available website with Apache Default Page, under 10000 port is Webmin webapp and under 20000 port is Usermin webapp.

I leaved this information for later, and moved on enumerating other services.

## 3. Enumeration

While reading source code of website under 80 port, i found Brainfuck encrypted password, so i decrypted it and got password.

<img width="1477" height="102" alt="{FE15D6AF-D41C-4C5D-B3F0-46239726A89B}" src="https://github.com/user-attachments/assets/067e12a8-7b02-4396-80f2-d8e9f613e22d" />

I also performed gobuster scan, but didn't found anything interesting.

So i started enumerating SMB using enum4linux.

**Command:** enum4linux -a TARGET-IP

**Results:** cyber (Username)

## 4. Initial Access

Now i have password and username, so i tried to login on Webmin and Usermin, and i successfully logged in Usermin.

While looking for anything interesting in the Usermin dashboard, i found a console icon and by this i could execute commands as cyber trough webapp, so i tried to get a reverse shell.

<img width="202" height="57" alt="{EDEBF333-507A-4090-BD42-6DC35327F184}" src="https://github.com/user-attachments/assets/4510aaa6-c8f8-45b4-9e87-b965db4f1090" />

I also got first flag. **Command:** cat user.txt

Using this exploit https://www.exploit-db.com/exploits/50234 i could generate reverse shell payload, so i downloaded this exploit, changed listen_ip and listen_port, and i runned exploit using this **Command:** python3 50234.py -u TARGET-IP -l cyber -p "Decrypted Password" and successfully generated reverse shell command, i started listener using nc -lvnp 4444 and pasted payload command in console in the webapp and got reverse shell.

