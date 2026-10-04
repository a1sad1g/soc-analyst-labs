# Tomcat Takeover Lab

A SOC investigation exercise: analyze a packet capture (PCAP) to reconstruct how an attacker discovered, compromised, and took over an Apache Tomcat web server.

---

## Scenario

The SOC team has identified suspicious activity on a web server within the company's intranet. To better understand the situation, they have captured network traffic for analysis. The PCAP file may contain evidence of malicious activities that led to the compromise of the Apache Tomcat web server. Your task is to analyze the PCAP file to understand the scope of the attack.

As a **SOC Analyst**, your task is to analyze the provided PCAP file to understand the attack from start to finish. Determine:

- Where the attacker came from
- How they found and probed the server
- How they gained access to the Tomcat management interface
- What they uploaded or executed on the server
- How they tried to maintain access

## Tools

| Tool | Purpose |
|------|---------|
| Wireshark | Packet and HTTP stream analysis |
| IP geolocation lookup | Determine the attacker's origin (for example, an IP lookup website) |

### Useful Wireshark Filters

```
ip.addr == <attacker_ip>                          # All traffic involving the attacker
tcp.flags.syn == 1 && tcp.flags.ack == 0          # SYN packets (port scan detection)
http                                              # All HTTP traffic
http.request.uri contains "manager"               # Access to the Tomcat Manager
http.authorization                                # Basic auth credentials in requests
http.request.method == "POST"                     # Uploads and form submissions
http.response.code == 401                         # Failed authentication
http.response.code == 200                         # Successful requests
http contains ".war" || http contains ".jsp"      # Deployed payloads
tcp.port == 8080                                  # Default Tomcat port
```
Tip: use *Follow > TCP Stream* on interesting HTTP packets to read full requests and responses.


## Methodology

1. **Triage the PCAP:** review *Statistics > Conversations* and *Protocol Hierarchy* to find the most active external host.
2. **Identify the attacker:** isolate the suspicious IP and note its geolocation.
3. **Detect scanning:** look for many SYN packets to different ports from one source.
4. **Follow the web activity:** inspect HTTP requests for enumeration, failed logins (401), and the first successful login.
5. **Recover credentials:** decode the `Authorization: Basic` header (Base64) from the successful request.
6. **Find the payload:** locate the POST upload and the file name, then follow the stream to see the content.
7. **Trace post-exploitation:** look for outbound connections from the server and commands run through the shell.

## Disclaimer

This lab is for **educational and defensive security training** only. Analyze the provided capture in an isolated environment.

