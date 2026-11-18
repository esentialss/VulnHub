# VulnHub Write-Up - Empire: Breakout

## 1. Basic Information

**Platform: VulnHub**

**Machine name: Empire: Breakout**

**Difficulty level: Easy**

## 2. Reconnaissance

First, I ran an nmap scan to identify open ports, running services, and their versions.

**Command:** nmap -sV -sC -p- -n -T5 TARGET-IP

**Results: 80/tcp open http, 139/tcp open netbios-ssn, 445/tcp open netbios-ssn, 10000/tcp open http, 20000/tcp open http**

<img width="690" height="291" alt="{8095E2B5-94D4-4F7F-840C-52C32C553CFA}" src="https://github.com/user-attachments/assets/d8b95c30-7d73-4a8d-9a51-46c41e1442ee" />

On port 80, there is a website with the Apache default page; on port 10000, there is the Webmin webapp; and on port 20000, there is the Usermin webapp.

I left this information for later and moved on to enumerating other services.

## 3. Enumeration

While reading the source code of the website on port 80, I found a Brainfuck-encrypted password, so I decrypted it and obtained the password.

<img width="1477" height="102" alt="{FE15D6AF-D41C-4C5D-B3F0-46239726A89B}" src="https://github.com/user-attachments/assets/067e12a8-7b02-4396-80f2-d8e9f613e22d" />

I also performed a gobuster scan, but I didn't find anything interesting.

So I started enumerating SMB using enum4linux.

**Command:** enum4linux -a TARGET-IP

**Results:** cyber (Username)

## 4. Initial Access

Now I had a password and a username, so I tried to log in to Webmin and Usermin, and I successfully logged in to Usermin.

While looking for anything interesting in the Usermin dashboard, I found a console icon, and through it I could execute commands as cyber via the webapp, so I tried to get a reverse shell.

I also got the first flag. Command: cat user.txt

<img width="202" height="57" alt="{EDEBF333-507A-4090-BD42-6DC35327F184}" src="https://github.com/user-attachments/assets/4510aaa6-c8f8-45b4-9e87-b965db4f1090" />

Using this exploit https://www.exploit-db.com/exploits/50234, I could generate a reverse shell payload, so I downloaded this exploit, changed listen_ip and listen_port, and ran the exploit using this Command: python3 50234.py -u TARGET-IP -l cyber -p "Decrypted Password" and successfully generated a reverse shell command. I started a listener using nc -lvnp 4444, pasted the payload command into the console in the webapp, and got a reverse shell.

I got a reverse shell as the cyber user.

## 5. Post-Exploitation Enumeration

I started post-exploitation enumeration using common commands such as id, whoami, sudo -l, etc.

While searching for SUID, I found this, but nothing worked.

<img width="358" height="183" alt="{2E655805-F04E-4E30-A3C8-7C7302A67082}" src="https://github.com/user-attachments/assets/aee5666a-cce6-4068-8d1e-4307c884f1a2" />

Later I found that tar is available on the target machine, so I started using it to get the root flag.

I could also read the /etc/shadow file, but cracking the root password took too long, so I focused on tar.

## 7. Privilege Escalation

Using tar, I could archive root files and then read their contents.

**Commands:**

/home/cyber/tar -cf /tmp/root-files.tar /root (Archiving root files and saving them in /tmp)

/home/cyber/tar -tvf /tmp/root-files.tar (Reading the contents)

**Results:**

<img width="629" height="298" alt="{AE77E646-623B-45EE-BD31-E242594DEDB7}" src="https://github.com/user-attachments/assets/7fc1f700-36a9-484b-8afd-3e72002bf9eb" />

We have the root flag, so we need to read it.

**Commands:**

mkdir -p /tmp/rootdata (Creating a directory for the files)

/home/cyber/tar -xf /tmp/root-files.tar -C /tmp/rootdata (Extracting the files)

And now we can successfully read the root flag using: cat /tmp/rootdata/root/rOOt.txt
