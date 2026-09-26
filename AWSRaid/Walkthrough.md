# AWSRaid — Investigation Walkthrough

## Lab Overview

**Platform:** CyberDefenders  
**Lab:** AWSRaid  
**SIEM:** Splunk  
**Data Source:** AWS CloudTrail

### Objective

The objective of this investigation is to analyze AWS CloudTrail activity,
identify the compromised account, trace the attacker's actions, and determine
the affected AWS resources.

---

# Investigation


### Question

Knowing which user account was compromised is essential for understanding the attacker's initial entry point into the environment. What is the username of the compromised user?

### Investigation

We begin by examining the AWS identities present in the CloudTrail data.


