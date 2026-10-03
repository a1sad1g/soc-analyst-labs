# PsExec Hunt Lab

A SOC investigation exercise: trace an attacker's lateral movement through a network using PsExec by analyzing a packet capture (PCAP).

---

## Scenario

The Intrusion Detection System (IDS) flagged suspicious **lateral movement activity involving PsExec**. This may indicate unauthorized access and movement across the network.

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





