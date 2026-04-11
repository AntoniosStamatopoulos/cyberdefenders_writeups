# XMLRAT Lab – Incident Analysis Report

## 1. Introduction

This report documents the methodology followed during the analysis of a network-based malware incident using a provided PCAP file. The objective was to identify the attack vector, extract malicious payloads, analyze execution techniques, and reconstruct the attack chain.

The investigation was conducted using network forensics, malware analysis, and threat intelligence techniques.

---

## 2. Methodology

### 2.1 Network Traffic Analysis

The analysis began with inspecting the PCAP file using Wireshark. HTTP traffic was filtered to identify suspicious outbound connections and potential payload delivery.

Filters used:

* `http`
* `http.request.method == "GET"`

This allowed identification of unusual requests to external IP addresses.

---

### 2.2 Identification of Initial Payload Delivery (Q1)

Suspicious HTTP GET requests were examined. One request stood out due to:

* Direct IP communication
* Non-standard port usage
* Retrieval of a file with a misleading extension

By reconstructing the request, the first-stage payload delivery URL was identified.

**Answer:**

```
http://45.126.209.4:222/mdm.jpg
```

---

### 2.3 Infrastructure Attribution (Q2)

The identified IP address was analyzed using threat intelligence platforms (e.g., AbuseIPDB, WHOIS lookup).

The hosting provider was determined based on ISP/organization data associated with the IP.

**Answer:**

```
reliablesite.net
```

---

### 2.4 Payload Extraction and Deobfuscation (Q3)

The downloaded file appeared to be a `.jpg`, but analysis revealed it contained encoded data rather than a valid image.

Steps performed:

1. Extracted the file from PCAP
2. Identified hex-encoded content
3. Used CyberChef with "From Hex" operation


The SHA256 hash was then calculated on the decoded executable.

**Answer:**

```
1eb7b02e18f67420f42b1d94e74f3b6289d92672a0fb1786c30c03d68e81d798
```

---

### 2.5 Malware Classification (Q4)

The extracted executable was analyzed using VirusTotal. The detection results were reviewed, specifically focusing on the classification provided by the Alibaba engine.

The malware family was identified accordingly.

**Answer:**

```
asyncrat
```

---

### 2.6 Static Analysis – Compilation Timestamp (Q5)

The executable’s metadata was examined in VirusTotal under the "Details" section.

The PE header provided the compilation timestamp, which indicates when the binary was built.

**Answer:**

```
2023-10-30 15:08
```

---

### 2.7 Execution Technique Analysis – LOLBin Identification (Q6)

The decoded script was analyzed to understand how the malware achieved execution.

Observations:

* Obfuscated strings using special characters (`#`)
* String reconstruction via concatenation
* Execution via legitimate Windows binary

After deobfuscation, the script revealed usage of a Living-Off-The-Land Binary (LOLBin) for stealth execution.

**Answer:**

```
C:\Windows\Microsoft.NET\Framework\v4.0.30319\RegSvcs.exe
```

---

### 2.8 Persistence and File Dropping Behavior (Q7)

To identify the files dropped by the script, the decoded content of the malicious script was manually inspected.

By examining each instance of this function and extracting the file paths provided as arguments, it was possible to determine which files were being written to disk. No additional execution or scripting was required, as the filenames were clearly visible within the decoded text.

This manual inspection revealed that the script creates three files, which are later used to execute and maintain persistence.

Answer:

Conted.ps1, Conted.bat, Conted.vbs



**Answer:**

```
Conted.ps1, Conted.bat, Conted.vbs
```

---

## 3. Key Findings

* The attacker used HTTP to deliver a staged payload disguised as an image file.
* The payload was obfuscated using hexadecimal encoding.
* The final malware was identified as AsyncRAT.
* Execution was achieved using a legitimate Windows binary (RegSvcs.exe).
* Persistence was established through scheduled tasks and dropped scripts.

---

## 4. Conclusion

This lab demonstrated a complete attack chain involving:

* Initial access via network-delivered payload
* Obfuscation and staged execution
* Use of LOLBins for stealth
* Persistence through scheduled task automation

The analysis reinforced practical skills in network forensics, malware deobfuscation, and attacker behavior identification.

---

## 5. Skills Applied

* Packet analysis using Wireshark
* Payload extraction from PCAP
* Data decoding and reconstruction
* Malware classification using threat intelligence platforms
* Script analysis and deobfuscation
* Identification of persistence mechanisms
* Mapping attacker techniques to real-world scenarios
