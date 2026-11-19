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

