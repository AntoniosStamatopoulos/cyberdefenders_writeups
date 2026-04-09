# CyberDefenders Lab – Lockdown (DFIR Analysis)

## Overview

This lab simulates a real-world security incident involving a compromised public-facing IIS server within a cloud environment. The scenario challenges the analyst to investigate suspicious outbound traffic, identify the intrusion vector, and reconstruct the attacker’s activity across multiple data sources.

## Objectives

The primary goal of the lab is to perform a full digital forensic and incident response (DFIR) investigation by analyzing network, memory, and malware artifacts. The analyst is required to:

* Identify the initial access vector and attacker behavior
* Analyze network traffic for reconnaissance and command-and-control activity
* Perform memory forensics to uncover processes, execution chains, and persistence mechanisms
* Conduct static malware analysis to understand obfuscation and threat characteristics
* Correlate findings across all artifacts to reconstruct the full attack lifecycle

## Provided Artifacts

The lab includes three key forensic artifacts:

* **PCAP file**: Captures network traffic related to the intrusion, including reconnaissance, exploitation, and outbound communication
* **Memory dump**: Contains a snapshot of the compromised system, enabling process analysis and detection of malicious execution
* **Malware sample**: A recovered executable used by the attacker, intended for static analysis and threat intelligence enrichment

## Skills Developed

This lab helps develop practical skills in:

* Network traffic analysis (Wireshark)
* Memory forensics (Volatility)
* Malware analysis (static techniques)
* Threat hunting and correlation
* MITRE ATT&CK mapping
* Incident reconstruction and reporting

## Tools Used

* Wireshark
* Volatility 3
* Detect It Easy (DIE) / PE analysis tools
* Strings / command-line utilities
* VirusTotal (for threat intelligence)

## Conclusion

The lab provides a comprehensive DFIR experience, combining multiple analysis techniques to simulate a realistic cyber intrusion investigation. It emphasizes the importance of correlating evidence across different sources to understand attacker behavior and improve defensive capabilities.
