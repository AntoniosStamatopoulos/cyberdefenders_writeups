# XMLRAT Lab – Network Traffic & Malware Analysis

## Overview

This lab focuses on analyzing network traffic to investigate a malware infection scenario. Using a provided PCAP file, the objective is to identify how the attacker delivered the payload, understand the execution flow, and analyze the behavior of the malware across multiple stages.

The exercise simulates a real-world incident response case where a compromised machine exhibits suspicious outbound communication. The analyst is required to reconstruct the attack chain and extract meaningful indicators from the captured traffic.

## Objectives

* Analyze PCAP data to identify malicious activity
* Detect the initial infection vector and payload delivery
* Investigate multi-stage malware execution
* Extract and analyze embedded or obfuscated payloads
* Understand attacker techniques and persistence mechanisms

## Tools & Technologies

* Wireshark (network traffic analysis)
* CyberChef (data decoding and deobfuscation)
* VirusTotal (malware intelligence and enrichment)


## Key Analysis Areas

* HTTP traffic inspection and object extraction
* Identification of suspicious requests and payload downloads
* Script deobfuscation techniques (e.g., hex decoding)
* Detection of Living-Off-The-Land Binaries (LOLBins)
* Malware staging and execution flow
* Scheduled tasks and persistence mechanisms

## Skills Developed

* Network forensics and packet analysis
* Malware analysis fundamentals
* Threat hunting in network traffic
* Deobfuscation and payload reconstruction
* Understanding of attacker techniques such as LOLBins and evasion
* Mapping observed behavior to MITRE ATT&CK techniques

## Disclaimer

This repository is intended for educational purposes only. The materials and techniques demonstrated are used in controlled lab environments for cybersecurity training and research.

## Outcome

By completing this lab, analysts gain practical experience in identifying and analyzing attack patterns and improve their ability to detect and respond to malware incidents.
