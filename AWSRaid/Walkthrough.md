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

**Knowing which user account was compromised is essential for understanding the attacker's initial entry point into the environment. What is the username of the compromised user?**

### Investigation

We begin by examining the AWS identities present in the CloudTrail data.

```spl
index="aws_cloudtrail" eventSource="signin.amazonaws.com" errorMessage="Failed authentication" | stats count by userIdentity.userName | sort -count
```
This search groups the CloudTrail events by user identity, allowing us to identify the accounts that generated activity in the AWS environment.

After identifying the available user accounts, we investigate their activity to determine which account was involved in the initial compromise.
```
index=aws-cloudtrail sourcetype=aws:cloudtrail
userIdentity.userName="[USERNAME]"
| table _time eventName eventSource sourceIPAddress userIdentity.userName requestParameters
| sort 0 _time
```
By reviewing the user's activity chronologically, we can identify the suspicious actions associated with the compromised account

**Result:** The compromised user account was: `helpdesk.luke`

### Question 2

**We must investigate the events following the initial compromise to understand the attacker's motives. What is the timestamp for the first access to an S3 object by the attacker?**

### Investigation

We continue the investigation using the compromised user identified in Question 1.

First, we filter the CloudTrail events to S3 activity performed by the compromised account and sort the events chronologically.
```
index=aws-cloudtrail sourcetype=aws:cloudtrail
userIdentity.userName="[COMPROMISED_USER]"
| search eventSource="s3.amazonaws.com"
| sort 0 _time
| table _time eventName requestParameters sourceIPAddress
```
This allows us to review the S3 activity performed by the compromised account in chronological order.

We then identify the first event that represents access to an S3 object, such as a GetObject operation.

```
index=aws-cloudtrail sourcetype=aws:cloudtrail
userIdentity.userName="[COMPROMISED_USER]"
eventSource="s3.amazonaws.com"
eventName="GetObject"
| sort 0 _time
| head 1
| table _time eventName requestParameters sourceIPAddress
```
The earliest event returned by this search represents the first recorded S3 object access by the attacker.

**Result:** The timestamp of the first S3 object access was: `2023-11-02 09:55`
### Question 3

**Among the S3 buckets accessed by the attacker, one contains a DWG file. What is the name of this bucket?**

### Investigation

We investigate the S3 object requests made by the attacker and examine the requested object paths.

```spl
index="aws_cloudtrail" "userIdentity.userName"="helpdesk.luke" eventSource="s3.amazonaws.com" eventName="GetObject" "*.dwg" | table _time, requestParameters.bucketName, requestParameters.key
```

### Question 4

**We've identified changes to a bucket's configuration that allowed public access, a significant security concern. What is the name of this particular S3 bucket?**

### Investigation 

We investigate S3 configuration changes and bucket access-control events.

![Bucket](screenshot/Bucket.png)

### Question 5

**Creating a new user account is a common tactic attackers use to establish persistence in a compromised environment. What is the username of the account created by the attacker?**

### Investigation

We search CloudTrail for IAM user-creation activity.

```spl
 index="aws_cloudtrail" "userIdentity.userName"="helpdesk.luke" eventCategory="Management" | search eventName="CreateUser" OR eventName="CreateLoginProfile"| table _time, eventName, requestParameters.userName
```

### Question 6

**Following account creation, the attacker added the account to a specific group. What is the name of the group to which the account was added?**

### Investigation

We investigate IAM events following the `CreateUser` event.

![User_Group](screenshot/User_Group.png)

The AddUserToGroup event contains the group to which the newly created
account was added. 

![Group_Name](screenshot/Group_Name.png)

In `requestParameters.groupName` we can see the name of the group was added to it

