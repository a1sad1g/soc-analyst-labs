## CyberDefenders — JetBrains
## Overview

This write-up documents my investigation of the JetBrains lab on CyberDefenders.

The investigation focuses on analyzing network traffic to identify how a web server was compromised and what actions the attacker performed after gaining access.

## Scenario
During a recent security incident, an attacker successfully exploited a vulnerability in our web server, allowing them to upload webshells and gain full control over the system. The attacker utilized the compromised web server as a launch point for further malicious activities, including data manipulation. 

As part of the investigation, You are provided with a packet capture (PCAP) of the network traffic during the attack to piece together the attack timeline and identify the methods used by the attacker. The goal is to determine the initial entry point, the attacker's tools and techniques, and the compromise's extent.
