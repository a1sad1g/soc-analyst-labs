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


### Question 1

Knowing which user account was compromised is essential for understanding the attacker's initial entry point into the environment. What is the username of the compromised user?

### Investigation

We begin by examining the AWS identities present in the CloudTrail data.

```spl
index="aws_cloudtrail" eventSource="signin.amazonaws.com" errorMessage="Failed authentication" | stats count by userIdentity.userName | sort -count
```

### Question 2

We must investigate the events following the initial compromise to understand the attacker's motives. What is the timestamp for the first access to an S3 object by the attacker?

### Investigation

After identifying the compromised account, we can focus on its S3 activity.

![Timestamp][screenshot/Timestamp.png]
