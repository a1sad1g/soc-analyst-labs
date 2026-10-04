# Tomcat Takeover Lab: Walkthrough

Step-by-step investigation of the PCAP, following the attacker from first contact to persistence.

## Step 0: Get Oriented

Before filtering, get a high-level view of the capture.

- **Statistics > Protocol Hierarchy:** confirm HTTP and TCP dominate.
- **Statistics > Conversations > IPv4:** find the external host with the most traffic to the server.
- **Statistics > Endpoints:** note the server IP and port (Tomcat usually listens on `8080`).

### Q1: Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?

**Goal:** identify the external host attacking the server.

**Approach:**
1. Open *Statistics > Conversations > IPv4* and sort by packets.
2. Identify the external IP with abnormal volume toward the server.
3. Confirm by filtering all of its traffic:
   ```
   ip.addr == 14.0.0.120
   ```

**Answer:** `14.0.0.120`

![Attacker_IP](Screenshot/Attacker_IP.png)

### Q2: Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?






