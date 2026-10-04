# PsExec Hunt Lab

A SOC investigation exercise: trace an attacker's lateral movement through a network using PsExec by analyzing a packet capture (PCAP).

---

## Scenario

An alert from the Intrusion Detection System (IDS) flagged suspicious lateral movement activity involving PsExec. This indicates potential unauthorized access and movement across the network. As a SOC Analyst, your task is to investigate the provided PCAP file to trace the attacker’s activities. Identify their entry point, the machines targeted, the extent of the breach, and any critical indicators that reveal their tactics and objectives within the compromised environment.

## Background: How PsExec Works

PsExec is a legitimate Sysinternals tool that attackers commonly abuse for lateral movement. A typical session looks like this:

1. Authenticate to the target over SMB (often NTLM) with valid credentials
2. Connect to the **ADMIN$** share and copy the service binary (`PSEXESVC.exe`)
3. Create and start a Windows service through the Service Control Manager
4. Connect to **IPC$** and use named pipes to send commands and receive output

## Tool

| Tool | Purpose |
|------|---------|
| Wireshark | Packet analysis of the PCAP |


### Useful Wireshark Filters

```
smb2                                 # All SMB2 traffic
smb2.cmd == 5                        # SMB2 Create requests (file/pipe access)
smb2.filename contains "PSEXESVC"    # PsExec service binary and pipes
ntlmssp.auth.username                # Authenticating account
ntlmssp.auth.hostname                # Workstation name from NTLM auth
smb2.tree                            # Share names (ADMIN$, IPC$)
dcerpc                               # Service Control Manager calls
```


## Methodology

1. **Open the PCAP** in Wireshark and review *Statistics > Conversations* and *Protocol Hierarchy* to find the busiest hosts and protocols.
2. **Filter for SMB2** and look for connections to administrative shares.
3. **Inspect NTLM authentication** to recover usernames and hostnames.
4. **Locate the service binary** being written and the service being created.
5. **Follow the traffic** to identify further targets after the first pivot.

## Relevant MITRE ATT&CK Techniques

| ID | Technique |
|----|-----------|
| T1021.002 | Remote Services: SMB/Windows Admin Shares |
| T1569.002 | System Services: Service Execution |
| T1078 | Valid Accounts |


## Disclaimer

This lab is for **educational and defensive security training** only. Analyze the provided capture in an isolated environment.


