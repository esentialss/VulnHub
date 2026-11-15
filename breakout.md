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

Under 80 port is available webapp with Apache Default Page, under 10000 port is Webmin webapp and under 20000 port is Usermin webapp.

I leaved this information for later
