# Penetration Testing Report

## VulnHub - Empire: Breakout

**Assessment Type:** Web Application and Linux Host Penetration Test

**Platform:** VulnHub

**Target:** Empire: Breakout

**Difficulty:** Easy

**Operating System:** Linux

**Assessment Status:** Completed

**Report Classification:** Technical Security Assessment - Lab Environment

# 1. Executive Summary

This report documents a penetration test conducted against the Empire: Breakout machine hosted on the VulnHub platform. The assessment focused on identifying exposed services, enumerating web applications, exploiting security vulnerabilities, obtaining initial access, and escalating privileges to the root user.

The assessment identified several weaknesses that, when chained together, enabled complete compromise  of the target system. The initial attack vector was the disclosure of a password in the source code of the website on port 80. The password was encrypted using the Brainfuck algorithm; however, after decryption, it enabled authentication to the Usermin application on port 20000. SMB enumeration revealed the username **cyber**, which, combined with the recovered password, allowed login to Usermin.

After gaining access to Usermin, the built-in console was used to execute commands as the **cyber** user. A public exploit (EDB-50234) was then used to generate a reverse shell payload, which resulted in an interactive system shell.

Further post-exploitation enumeration revealed the ability to read the **/etc/shadow** file and the presence of the **tar** utility on the target machine. By using **tar** to archive root files and subsequently extract them, access to the root flag was obtained. 

The attack chain demonstrates how weaknesses in secret storage in source code, insufficient privilege segmentation, and unsafe configurations of privileged utilities can combine to compromise an entire system.

**Overall assessment:** The target was fully compromised within the authorized lab environment, with both user-level and root-level access obtained.

# 2. Scope and Objectives

## 2.1 Scope

The assessment was limited to the Empire: Breakout machine on the VulnHub platform.

**Attribute** | **Details**

Target | Empire: Breakout

Target IP | TARGET-IP

Operating system | Linux

Exposed services | HTTP (80), NetBIOS (139), SMB (445), HTTP (10000), HTTP (20000)

Applications | Apache (80), Webmin (10000), Usermin (20000)

Assessment environment | Authorized CTF lab

## 2.2 Objectives

The primary objectives were to:

* Identify exposed network services and potential attack vectors.

* Enumerate web applications and assess their authentication controls.

* Identify and exploit vulnerabilities that could provide initial access.

* Investigate local services and recover potentially exposed credentials.

* Evaluate privilege escalation opportunities.

* Demonstrate the impact of the identified weaknesses using proof-of-compromise evidence.

