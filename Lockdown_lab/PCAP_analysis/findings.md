# PCAP Analysis Report – Lockdown Lab

## Overview

This report presents the analysis of the PCAP file from the Lockdown lab. The investigation focuses on identifying attacker activity across different stages of the intrusion, including reconnaissance, enumeration, lateral movement, payload delivery, and command-and-control communication.

---

## Q1 – Source of Reconnaissance Traffic

**Answer:** 10.0.2.4

### Methodology

HTTP traffic was analyzed in Wireshark to identify abnormal patterns. A single IP address was observed sending a high volume of requests within a short time frame. These requests targeted multiple endpoints on the IIS server and included probing patterns such as directory enumeration and service checks.

### Conclusion

The traffic pattern indicates automated reconnaissance activity. The attacker used scanning techniques to discover accessible resources and potential vulnerabilities on the target system.

---

## Q2 – Targeted Enumeration Technique (MITRE ATT&CK)

**Answer:** T1046

### Methodology

Following the reconnaissance phase, the traffic showed more focused interaction with a specific service (HTTP/IIS). The attacker transitioned from broad scanning to targeted probing of the exposed service.

### Conclusion

This behavior aligns with **MITRE ATT&CK T1046 – Network Service Discovery**, where attackers identify and interact with available services to prepare for exploitation.

---

## Q3 – SMB Share Enumeration

**Answer:**
\10.0.2.15\IPC$, \10.0.2.15\Documents

### Methodology

SMB traffic (TCP port 445) was filtered and analyzed. By examining SMB2 Tree Connect requests, the first shares accessed by the attacker were identified. These requests reveal which network resources the attacker attempted to interact with.

### Conclusion

The attacker enumerated available SMB shares, indicating movement from external probing to internal exploration of the compromised system.

---

## Q4 – Malicious File Upload via SMB

**Answer:**
shell.aspx, 1015024

### Methodology

SMB file operations were analyzed by inspecting SMB2 Create and Write requests. A file upload was identified through:

* A Create Request for a new file
* A Write Request transferring data to that file, including the byte length

The file extension (.aspx) indicates a web-accessible script.

### Conclusion

The attacker uploaded a malicious web shell to the system, enabling remote command execution via the IIS server. This marks the transition from enumeration to active exploitation.

---

## Q5 – Reverse Shell Communication

**Answer:** 4443

### Methodology

Network traffic was filtered to identify outbound connections from the compromised host. A persistent TCP connection from the victim to the attacker was observed using a non-standard port.

### Conclusion

The attacker established a reverse shell connection using port 4443. This port choice mimics legitimate HTTPS traffic, helping evade basic firewall detection while maintaining remote control of the system.

---

## Summary

The PCAP analysis reveals a complete attack chain:

1. **Reconnaissance** – Automated scanning from an external host
2. **Service Enumeration** – Targeted interaction with the IIS service
3. **SMB Enumeration** – Discovery of internal shares
4. **Payload Deployment** – Upload of a malicious web shell
5. **Command & Control** – Establishment of a reverse shell

This sequence demonstrates a realistic intrusion scenario, highlighting how attackers move from initial access to full system compromise using common techniques and protocols.
