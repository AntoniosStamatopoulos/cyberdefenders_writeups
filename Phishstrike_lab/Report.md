# PhishStrike Lab – Investigation Report

## 1. Introduction

This report documents the analysis of a phishing email campaign targeting an educational institution. The objective was to identify malicious indicators, analyze the attack chain, and understand how the threat actor delivers and executes malware.

---

## 2. Methodology

### 2.1 Email Analysis

The email file was safely opened using a text editor (Notepad) to inspect its raw content. This allowed direct analysis of:

* Email headers
* Sender information
* Embedded URLs

Authentication mechanisms such as SPF, DKIM, and DMARC were manually reviewed.

---

### 2.2 IOC Extraction

Indicators of Compromise (IOCs) were extracted from the email, including:

* IP addresses
* URLs
* File names
* Email metadata

---

### 2.3 Threat Intelligence Analysis

The extracted IOCs were analyzed using:

* VirusTotal
* URLhaus
* tria.ge sandbox reports

A pivoting approach was used:

* URL → IP
* IP → malware samples
* Samples → hashes and behavior

---

### 2.4 Malware Behavior Analysis

Public sandbox reports were used to analyze:

* Network communication
* Persistence mechanisms
* Command execution
* Data exfiltration

---

## 3. Findings

### 3.1 Email Analysis Findings

* Sender IP (SPF softfail, DKIM fail): **18.208.22.104**
* Return-Path: **[erikajohana.lopez@uptc.edu.co](mailto:erikajohana.lopez@uptc.edu.co)**

These findings indicate email spoofing and lack of proper authentication.

---

### 3.2 Malicious Infrastructure

* Malicious hosting IP: **107.175.247.199**
* Malicious URL (initial payload):
  **http://107.175.247.199/loader/install.exe**

The infrastructure is associated with multiple malware samples.

---

### 3.3 Malware Family Identification

* Cryptocurrency mining malware family: **CoinMiner**

The malicious infrastructure is used to distribute multiple payloads, including mining malware.

---

### 3.4 Network Communication

* Malware requested URL:
  **http://ripley.studio/loader/uploads/Qanjttrbv.jpeg**

This indicates outbound communication to external infrastructure.

---

### 3.5 Persistence Mechanism

* Registry autorun executable: **Jzwvix.exe**

The malware establishes persistence through Windows registry autorun keys.

---

### 3.6 File Hash Identification

* SHA-256 hash:
  **bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539**

This hash corresponds to the BitRAT malware sample.

---

### 3.7 Malware Delivery Chain

* Loader retrieves payload from:
  **http://107.175.247.199/loader/server.exe**

This represents the second-stage payload delivery.

---

### 3.8 Execution Evasion Technique

* PowerShell delay: **50 seconds**

The delay is used to evade sandbox and automated detection mechanisms.

---

### 3.9 Command and Control (C2)

* C2 Domain: **gh9st.mywire.org**

This domain is used for remote communication and attacker control.

---

### 3.10 Data Exfiltration

* Telegram Bot ID: **5610920260**

The malware uses Telegram API for data exfiltration and communication.

---

## 4. Attack Chain Summary

1. Phishing email is delivered to the victim
2. User clicks malicious link
3. Loader (install.exe) is downloaded
4. Loader retrieves second-stage payload
5. Malware executes and establishes persistence
6. Malware communicates with C2 infrastructure
7. Data is exfiltrated via external services (Telegram)

---

## 5. Conclusion

The phishing campaign demonstrates a multi-stage attack combining social engineering, malware delivery, persistence mechanisms, and external communication channels.

The attacker leveraged:

* Email spoofing
* Malicious hosting infrastructure
* Multi-stage payload delivery
* Registry-based persistence
* Command and control communication
* Data exfiltration via Telegram

---

## 6. Recommendations

Note: This section is included to demonstrate understanding of defensive security practices and the ability to translate findings into actionable mitigation strategies.

* Enforce SPF, DKIM, and DMARC policies
* Implement email filtering solutions
* Educate users on phishing detection
* Block malicious IPs and domains
* Monitor registry changes for persistence
* Inspect outbound traffic for suspicious activity
* Use sandboxing for unknown files and URLs

---

## 7. Note

This report is based on a simulated lab environment and is intended for educational purposes only.
