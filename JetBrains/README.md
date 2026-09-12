## CyberDefenders — JetBrains
## ⚠️ Disclaimer ⚠️
This write-up is for educational purposes only. It is meant to explain the thought process and steps taken to solve the challenge.

Please do not simply copy and paste the answers, as this may slow down your technical growth and prevent you from building real problem-solving skills.

Always try the challenge on your own first. Use this write-up only as a guide to understand the approach, not as a shortcut.
Real skills are built through practice, trial and error, and persistence.

## CyberDefender Lab Write-ups
[CyberDefender](https://cyberdefenders.org/blueteam-ctf-challenges/jetbrains)
## Overview

This write-up documents my investigation of the JetBrains lab on CyberDefenders.

The investigation focuses on analyzing network traffic to identify how a web server was compromised and what actions the attacker performed after gaining access.

## Lab Information
**1- Lab Title:** JetBrains.

**2- Course:** Network Forensics.

## Objective
The purpose of the current experiment is to conduct a thorough analysis of the collected network data traffic and recreate the entire attack cycle conducted by the adversary on the compromised web server.
With the help of applications like Wireshark, NetworkMiner, and Brim, the primary aim was to:

**1- Ascertain the source IP address of the attacker.**

**2- Establish the vulnerable service and corresponding CVE.**

**3- Monitor the activities of the adversary post-compromise.**

**4- Find out about any persistence techniques such as web shells and backdoors.**

**5- Examine attacker commands and post-compromise actions.**

**6- Relate observed behaviors to the MITRE ATT&CK framework.**

## Q1: Identifying the attacker's IP address helps trace the source and stop further attacks. What is the attacker's IP address?

I identified the attacker's IP address by analyzing the HTTP traffic targeting the web server.

The suspicious HTTP POST request used to upload a file originated from:

**Attacker IP:** `23.158.56.196`

The request was sent to the compromised web server at `172.31.25.119`.

![Attacker IP - HTTP POST](screenshot/Screenshot%20From%202026-09-12%2001-32-04.png)

The packet shows `23.158.56.196` as the source IP and `172.31.25.119` as the destination. The HTTP POST request targets `/admin/pluginUpload.html`, indicating file-upload activity.

## Q2: To identify potential vulnerability exploitation, what version of our web server service is running?

We now know the IP-address of the attacker, so this will allow for some more specific searching. Let’s filter for all HTTP requests from this IP-address:
`ip.src == 23.158.56.196 && http`
In we can see there are request from this filter and the interested one was `GET /hax?jsp=/app/rest/server;.jsp` this could gave us more about the server version so by Right click into the packet and follow the http stream:

![Server version](screenshot/Screenshot%20From%202026-09-12%2001-32-52.png)

In the response body we can see the server version




