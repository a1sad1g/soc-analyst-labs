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

This alert comes because there are a Possible LFI Attack detected on a URL request `https://172.16.17.13/?file=../../../../etc/passwd` and this can be a path traversal vulnerability this bug is server-side vulnerability can lead to sensitive information about the server



