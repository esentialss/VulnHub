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

## 3. Methodology 

The assessment followed a structured penetration testing workflow consisting of the following phases:

1. **Reconnaissance:** Identification of open ports and running services.

2. **Enumeration:** Discover of application endpoints, source code analysis, and SMB service enumeration.

3. **Exploitation:** Abuse of the disclosed password and Usermin vulnerability to obtain access.

4. **Post-exploitation:** Enumeration of the compromised host and collection of the user flag.

5. **Credential discovery:** Analysis of accessible files and services.

6. **Privilege escalation:** Abuse of the **tar** utility to obtain access to root files.

7. **Verification:** Confirmation of elevated privileges and retrieval of the root flag.

**Tools Used:**

* Nmap

* Gobuster

* Enum4linux

* Netcat

* Web browser

* Exploit-DB (EDB-50234)

* Linux command-line utilities

# 4. Reconnaissance and Service Enumeration

## 4.1 Port Scanning 

An initial Nmap scan was conducted to identify open ports, service versions, and common service-related information.

**Command:** nmap -sV -sC -p- -n -T5 TARGET-IP

**Results:**

**Port** | **Service** | **Status**

80/tcp | HTTP | Open

139/tcp | NetBIOS-SSN | Open

445/tcp | NetBIOS-SSN (SMB) | Open

10000/tcp HTTP (Webmin) | Open

20000/tcp HTTP (Usermin) | Open

**Evidence:** Nmap scan output.

<img width="690" height="291" alt="image" src="https://github.com/user-attachments/assets/89fd29db-e6a1-457d-9d31-5acb30fb2c0b" />

Port 80 hosted the default Apache page, port 10000 hosted the Webmin web application, and port 20000 hosted the Usermin web application.

# 5. Web Application Enumeration

## 5.1 Source Code Analysis 

While reviewing the source code of the website on port 80, a password encrypted using the Brainfuck algorithm was found. After decryption, the user password was obtained.

**Evidence:** Encrypted password in the source code.

<img width="1477" height="102" alt="image" src="https://github.com/user-attachments/assets/9d3c77bf-4d6e-4898-b799-f7d2f75b2432" />

## 5.2 Directory Enumeration 

A Gobuster scan was performed, which did not reveal any interesting paths.

## 5.3 SMB Enumeration 

SMB enumeration was initiated using enum4linux.

**Command:** **enum4linux -a TARGET-IP**

**Results:** The username **cyber** was disclosed.

# 6. Initial Access 

## 6.1 Usermin Authentication

With the password and username in hand, login attempts were made against Webmin and Usermin. Authentication was successful in the Usermin application.

## 6.1 Usermin Console Exploitation

Within the Usermin dashboard, a console icon was found that allowed command execution as the **cyber** user. This enabled an attempt to obtain a reverse shell.

**Evidence: User flag.**

<img width="202" height="57" alt="image" src="https://github.com/user-attachments/assets/c3ae41d9-371a-4063-b3b4-80c01d2828fc" />

**Command: cat user.txt**

## 6.3 Reverse Shell Acquisition

A public exploit available at the following URL was used: https://www.exploit-db.com/exploits/50234

The exploit allowed the generation of a reverse shell payload. After downloading the exploit, the **listen_ip** and **listen_port** parameters were modified, nad the exploit was executed:

**Command:** python3 50234.py -u TARGET-IP -l cyber -p "DECRYPTED_PASSWORD"

A reverse shell command was generated, a listener was started:

**Command: nc -lvnp 4444**

The payload was then pasted into the console in the Usermin application, resulting in an interactive shell as the **cyber** user.

**Result:** A reverse shell was obtained as the cyber user.

**Finding:** Disclosure of credentials in source code and insufficient execution restrictions in the Usermin console.

# 7. Post-Exploitation Enumeration

Post-exploitation enumeration was initiated using common commands such as **id**, **whoami**, **sudo -l**, etc.

While searching for SUID files, potential vectors were identified, but none proved successful.

**Evidence:** SUID search results.

<img width="358" height="183" alt="image" src="https://github.com/user-attachments/assets/02e29cfa-a778-4b01-ac5f-884192058810" />

It was determined that the **tar** utility was available on the target machine. This made it possible to read the **/etc/shadow** file; however, cracking the root password took too long, so focus shifted to exploiting **tar**.

# 8. Privilege Escalation

## 8.1 Using tar to Archive Root Files

Using **tar**, root files were archived, and their contents were when read.

**Commands:**

/home/cyber/tar -cf /tmp/root-files.tar /root

/home/cyber/tar -tvf /tmp/root-files.tar

**Results:** Access to the contents of the **/root** directory was obtained.

**Evidence:** List of files in the archive.

<img width="629" height="298" alt="image" src="https://github.com/user-attachments/assets/b46ef52b-73fb-441e-b871-6d22e3255424" />

## 8.2 Reading the Root Flag

A target directory was created, and the files were extracted from the archive.

**Commands:**

mkdir -p /tmp/rootdata

/home/cyber/tar -xf /tmp/root-files.tar -C /tmp/rootdata

The root flag was then read:

**Command:** cat /tmp/rootdata/root/rOOt.txt

**Result:** The root flag was obtained, confirming full system access.

**Finding:** Privilege escalation through a misconfigured **tar** utility that allowed access to root files.

# 9. Findings and Risk Assessment

The following findings summarize the security weaknesses identified during the assessment. Severity ratings are provisional qualitative assessments based on demonstrated impact in the lab environment; they are not formal CVSS scores.

## F-01: Password Disclosure in Website Source Code

**Suggested severity:** High 

**Description:** The source code of the website on port 80 contained a password encrypted using the Brainfuck algorithm, which, after decryption, enabled authentication to the Usermin application.

**Impact:** Unauthorized access to the Usermin application and the ability to execute commands as the **cyber** user.

**Evidence:** Encrypted password found in the source code; successful login to Usermin.

**Recommendations:**

* Never store passwords or secrets in application source code.

* Use dedicated secret management mechanisms (e.g.,a vault).

* Perform regular code scanning for disclosed secrets.

* Rotate all exposed credentials.

## F-02: Insufficient Execution Restrictions in the Usermin Console

**Suggested severity:** High

**Description:** The Usermin console allowed arbitrary command execution as the **cyber** user, which enabled obtaining a reverse shell.

**Impact:** Remote code execution in the security context of the **cyber** user, leading to initial host compromise.

**Evidence:** Successful execution of a reverse shell payload through the Usermin console.

**Recommendations:**

* Restrict access to the Usermin console to essentials functions only.

* Enforce the principle of least privilege for application accounts.

* Consider disabling the console for non-administrative accounts.

* Monitor and log actions performed through the console.

## F-03: Misconfiguration of the tar Utility Enabling Privilege Escalation

**Suggested severity:** Critical

**Description:** The **tar** utility was available to the **cyber** user nad allowed archiving and reading of root files, including the root flag.

**Impact:** Privilege escalation to root and complete system compromise.

**Evidence:** Successful archiving and extraction of files from the **/root** directory and retrieval of the root flag.

**Recommendations:**

* Remove unnecessary permissions for utilities such as **tar** for unprivileged accounts.

* Review the sudoers configuration for excessive permissions.

* Enforce the principle of least privilege for all system accounts.

* Consider using dedicated, restricted backup tools instead of general-purpose archiving utilities. 

* Regularly audit file and directory pemissions


## 10. Attack Chain Summary

The successful attack can be summarized as follows:

1. **Reconnaissance:** Identified open ports 80, 139, 445, 10000 and 20000.

2. **Web enumeration:** Found an encrypted password in the source code of the website on port 80.

3. **SMB enumeration:** Disclosed the username **cyber**.

4. **Initial access:** Decrypted the password and logged in to Usermin.

5. **Shell acquisition:** Used the Usermin console and exploit EDB-50234 to obtain a reverse shell as **cyber**.

6. **Post-exploitation:** Discovered access to **tar** and the ability to read root files.

7. **Privilege escalation:** Used **tar** to archive and extract root files.

8. **Objective completion:** Retrieved both the user flag and the root flag.

## 11. Conclusion

The assessment demonstrated that multiple security weaknesses in the Empire: Breakout environment could be chained to achieve complete system compromise.

The initial weakness of password disclosure in source code enabled access to the Usermin application. Insufficient Restrictions in the Usermin console allowed a reverse shell to be obtained. Subsequently, a misconfiguration of **tar** utility enabled privilege escalation to root.

The most significant lesson from this assessment is that application security, secret management, and operating system hardening must be addressed together. A single vulnerability may provide limited access, but several weaknesses across different layers can create a direct path to full compromise.

**Remediation priorities:**

1. Remove passwords and secrets from source code.

2. Restrict Usermin console permissions.

3. Remove unsafe permission for the **tar** utility.

4. Review the sudoers configuration and system account permissions.

**Final result:** Successful compromise of the application and underlying Linux host, including verified root-level access, within the authorized VulnHub lab environment.
