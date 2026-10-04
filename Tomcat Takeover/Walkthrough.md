# Tomcat Takeover Lab: Walkthrough

Step-by-step investigation of the PCAP, following the attacker from first contact to persistence.

## Step 0: Get Oriented

Before filtering, get a high-level view of the capture.

- **Statistics > Protocol Hierarchy:** confirm HTTP and TCP dominate.
- **Statistics > Conversations > IPv4:** find the external host with the most traffic to the server.
- **Statistics > Endpoints:** note the server IP and port (Tomcat usually listens on `8080`).

### Q1: Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?

**Goal:** identify the external host attacking the server.

1. Open *Statistics > Conversations > IPv4* and sort by packets.
2. Identify the external IP with abnormal volume toward the server.

**Answer:** `14.0.0.120`

![Attacker_IP](Screenshot/Attacker_IP.png)

### Q2: Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?

**Goal:** find where the attacker's IP is located.

1. Use any Threat Intel website that you search on it about the attacker ip (like [IPINFO](https://ipinfo.io))
2. And after that you can see the country of the attacker

![Country](Screenshot/Country_of_the_attacker.png)

**Answer:** `China`

### Q3: From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?







