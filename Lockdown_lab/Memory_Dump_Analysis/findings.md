# Memory Analysis Report – Lockdown Lab

## Overview

This report presents the analysis of the memory dump from the compromised IIS server. The goal of this analysis is to identify evidence of execution, persistence mechanisms, and process relationships that reveal attacker activity within the system.

All analysis was performed using Volatility 3.

---

## Q6 – Kernel Base Address

**Answer:** 0xf80079213000

### Methodology

The memory image was analyzed using the `windows.info` plugin in Volatility. This plugin provides key system-level information, including the kernel base address.

### Conclusion

The kernel base address confirms the successful parsing of the memory image and provides essential context for further forensic analysis. It verifies that the memory snapshot is valid and can be used for deeper investigation.

---

## Q7 – Persistence Mechanism

**Answer:**
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\updatenow.exe, T1547

### Methodology

Process analysis was conducted using the `windows.pstree` and `windows.cmdline` plugins. A suspicious executable was identified running outside the normal IIS directory structure.

Key observations:

* The executable was launched by a legitimate process chain
* It resided in the Windows Startup folder
* The location is commonly used for persistence

### Conclusion

The attacker established persistence by placing a malicious executable in the Startup folder. This ensures execution upon user logon and aligns with **MITRE ATT&CK T1547 – Boot or Logon Autostart Execution**.

---

## Q8 – Process Responsible for Reverse Shell

**Answer:**
w3wp.exe, 4332

### Methodology

The process tree (`windows.pstree`) was analyzed to identify relationships between processes. The IIS worker process (`w3wp.exe`) was observed spawning the malicious executable.

By correlating this with network activity from the PCAP analysis, it was determined that this process was responsible for handling the reverse shell communication.

### Conclusion

The built-in IIS worker process (`w3wp.exe`) was leveraged by the attacker to execute the malicious payload and maintain outbound communication. This demonstrates abuse of legitimate services to evade detection and maintain control over the compromised system.

---

## Summary

The memory analysis reveals key post-exploitation activities:

1. **System Context Validation** – Identification of kernel base address
2. **Persistence Establishment** – Malicious executable placed in Startup folder
3. **Execution Chain** – Legitimate IIS process used to launch the payload
4. **C2 Facilitation** – Reverse shell handled through a trusted system process

This analysis highlights how attackers leverage legitimate processes and persistence mechanisms to maintain access and evade detection within a compromised system.
