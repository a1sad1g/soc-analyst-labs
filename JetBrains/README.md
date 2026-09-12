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
The filter returned several HTTP requests. One request was particularly interesting `GET /hax?jsp=/app/rest/server;.jsp` This request could reveal additional information about the server, including its version. I right-clicked the packet and selected Follow → HTTP Stream to inspect the complete HTTP conversation.

![Server version](screenshot/Screenshot%20From%202026-09-12%2001-32-52.png)

In the response body we can see the server version

## Q3: After identifying the version of our web server service, what CVE number corresponds to the vulnerability the attacker exploited?

After identifying the web server as JetBrains TeamCity version `2023.11.3`, I searched for known vulnerabilities affecting this version.

Using the Rapid7 website, I identified the CVE associated with the exploited vulnerability:

![CVE](screenshot/Screenshot%20From%202026-09-12%2001-34-56.png)

CVE-2024-27198 is a critical authentication bypass vulnerability in JetBrains TeamCity that can allow an unauthenticated attacker to access protected functionality.

## Q4: What credentials did the attacker successfully use for Basic Auth against the TeamCity server? 

After enter the filter `http.request.method == POST` and collect the POST request contain `/hax?jsp=/app/rest/users;.jsp`. I then followed the HTTP conversation by selecting Follow → HTTP Stream.

![username:password](screenshot/Screenshot%20From%202026-09-12%2001-37-59.png)

While analyzing the HTTP stream, I found a username and password exposed in the request. This shows that authentication credentials were exposed in the captured HTTP traffic.

## Q5: The attacker uploaded a webshell to ensure his access to the system. What is the name of the file that the attacker uploaded?

Now that we have identified the exposed credentials, the next step was to identify the webshell uploaded by the attacker.

I used the same filter `http.request.method == POST`, I then analyzed the POST requests and identified the following endpoint: `/admin/pluginUpload.html`, This endpoint was particularly suspicious because it was used to upload a file to the TeamCity server.
I right-clicked the request and selected Follow → HTTP Stream to inspect the complete HTTP conversation.

![name_of_file](screenshot/Screenshot%20From%202026-09-12%2001-48-34.png)


By analyzing the HTTP stream, I identified the filename of the file uploaded by the attacker.

## Q6: When did the attacker execute their first command via the web shell?

After identifying the uploaded webshell, I filtered the traffic for POST requests and searched for requests to the uploaded `.jsp` file.

The first request to the webshell was: `POST /plugins/NSt8bHTg/NSt8bHTg.jsp` I then inspected the request contents and found the following command: `cmd=ls`

![timestamp](screenshot/Screenshot%20From%202026-09-12%2002-00-56.png)

The packet timestamp shows that the attacker executed their first command through the webshell at: `2024-06-30 08:03:57.620161`

## Q7: The attacker tampered with a text file that contained the credentials of the admin user of the webserver. What new username and password did the attacker write in the file?



