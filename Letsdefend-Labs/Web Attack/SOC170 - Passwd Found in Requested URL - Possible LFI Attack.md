# SOC170 - Passwd Found in Requested URL - Possible LFI Attack

| | |
|---|---|
| **Platform** | LetsDefend |
| **Event ID** | 120 |
| **Rule** | SOC170 - Passwd Found in Requested URL - Possible LFI Attack |
| **Level** | Security Analyst |

## 1. Alert Details

![Alert](Images/Alert_Details.png)

## Step 1 - Understand Why the alert Was Triggered

This alert comes because there are a Possible LFI Attack detected on a URL request `https://172.16.17.13/?file=../../../../etc/passwd` and this can be a path traversal vulnerability, This bug is server-side vulnerability can lead to sensitive information about the server

## Step 2 - Collect Data

First the ownership of the source IP is `106.55.45.162`, Destination IP is `172.16.17.13` and the host name of destination IP is `WebServer1006` and traffic came from the internet not from the company network
I searched about the source IP in virustotal and result for it was not malicious

![virustotal](Images/virustotal.png)

## Step 3 - Is Traffic Malicious

Based on the Request URL that came from the internet it's a malicious traffic because it's contained a payload that can lead to sensitive information about the server

## Step 4 - What is the Attack Type

Based on this payload `../../../../etc/passwd` it's considered as an **LFI Attack** can lead to sensitive information about the server

## Step 5 - Check Whether the Attack Was Successful

I searched in Log Management with the source address to know the response of the request that contained the malicious URL and based on the information that I found it the size of the response is `0` and status code return as `500` and this is a error from server that can't understand the request that was send

![Photo](Images/Raw_Log.png)






