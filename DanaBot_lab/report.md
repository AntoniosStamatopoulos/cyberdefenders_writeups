# CyberDefenders SOC Lab - PCAP Incident Investigation Report

## Scenario

The SOC team detected suspicious activity in network traffic indicating that a machine inside the environment had been compromised. The investigation was based on PCAP analysis and threat intelligence to determine how the breach occurred, identify the malicious files involved, and document the attacker infrastructure.

## Executive Summary

Analysis of the network capture revealed that the host `10.2.14.101` communicated with a suspicious external IP address, `62.173.142.148`, during the initial stage of the compromise. The victim downloaded a malicious JavaScript file named `allegato_708.js`, which acted as an initial access payload/dropper.

The JavaScript file was executed through Windows Script Host, most likely using `wscript.exe`. Once executed, it downloaded a second-stage malicious payload from attacker-controlled infrastructure. The second-stage file was a DLL, retrieved as `resources.dll` from the domain `soundata.top`.

## Key Findings

| Item | Finding |
|---|---|
| Victim host | `10.2.14.101` |
| Attacker IP address | `62.173.142.148` |
| Initial malicious file | `allegato_708.js` |
| Initial access URL | `/login.php` |
| SHA-256 of initial file | `847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268` |
| Process used to execute initial file | `wscript.exe` |
| Second malicious file extension | `.dll` |
| Second-stage payload | `resources.dll` |
| Second-stage domain | `soundata.top` |
| MD5 of second malicious file | `e758e07113016aca55d9eda2b0ffeebe` |

## Timeline of Events

### 1. Initial Network Contact

The internal host `10.2.14.101` initiated an HTTP connection to the external IP address `62.173.142.148`.

Observed request:

```http
GET /login.php HTTP/1.1
Host: 62.173.142.148
```

Although the requested resource was `/login.php`, the HTTP response delivered a JavaScript file named:

```text
allegato_708.js
```

This indicates that the PHP endpoint was used as a delivery mechanism for the initial malicious file.

### 2. Initial Malicious JavaScript Payload

The file `allegato_708.js` was heavily obfuscated. The script contained JavaScript code designed to run on Windows through Windows Script Host. Relevant indicators included:

```javascript
WScript.CreateObject(...)
ActiveXObject(...)
WScript.ScriptFullName
```

These artifacts indicate that the script was intended to be executed by Windows Script Host, typically through:

```text
wscript.exe
```

The SHA-256 hash of the malicious JavaScript file was:

```text
847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268
```

### 3. Second-Stage Payload Download

After execution, the JavaScript dropper attempted to download an additional payload from the attacker infrastructure.

The second-stage payload was identified as:

```text
resources.dll
```

Downloaded from:

```text
http://soundata.top/resources.dll
```

The second malicious file used the `.dll` extension.

The MD5 hash of the second-stage payload was:

```text
e758e07113016aca55d9eda2b0ffeebe
```

### 4. Execution Flow

The observed attack chain can be summarized as follows:

```text
Victim host 10.2.14.101
        |
        | HTTP GET /login.php
        v
Attacker server 62.173.142.148
        |
        | Delivers allegato_708.js
        v
JavaScript executed via wscript.exe
        |
        | Downloads second-stage DLL
        v
http://soundata.top/resources.dll
        |
        | DLL payload saved and executed
        v
Host compromise / follow-on malicious activity
```

## Indicators of Compromise

### Network Indicators

| Type | Indicator |
|---|---|
| IP address | `62.173.142.148` |
| Domain | `soundata.top` |
| URL path | `/login.php` |
| URL | `http://soundata.top/resources.dll` |

### File Indicators

| File | Type | Hash |
|---|---|---|
| `allegato_708.js` | JavaScript dropper | SHA-256: `847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268` |
| `resources.dll` | DLL payload | MD5: `e758e07113016aca55d9eda2b0ffeebe` |

### Host-Based Indicators

| Indicator | Description |
|---|---|
| `wscript.exe` | Process used to execute the malicious JavaScript file |
| `rundll32.exe` | Possible execution utility for DLL payloads |
| Temporary directory writes | The JavaScript generated a random filename and saved the second-stage payload to a temporary path |

## Wireshark Investigation Notes

Useful filters for reproducing the findings:

### Identify HTTP traffic related to the attacker IP

```wireshark
ip.addr == 62.173.142.148 && http
```

### Locate the initial malicious download

```wireshark
http.request.uri contains "login.php"
```

### Locate the second-stage DLL request

```wireshark
http.request.uri contains "resources.dll"
```

### Review all HTTP traffic from the victim host

```wireshark
ip.addr == 10.2.14.101 && http
```

### Export malicious files from the PCAP

In Wireshark:

```text
File -> Export Objects -> HTTP
```

Save suspicious files with safe extensions such as:

```text
allegato_708.js.disabled
resources.dll.disabled
```

This prevents accidental execution while preserving the file contents for hashing.

## Hashing Commands

### Windows PowerShell

```powershell
Get-FileHash .\allegato_708.js.disabled -Algorithm SHA256
Get-FileHash .\resources.dll.disabled -Algorithm MD5
```

### Linux

```bash
sha256sum allegato_708.js.disabled
md5sum resources.dll.disabled
```

## Conclusion

The compromise began when the victim host `10.2.14.101` downloaded a malicious JavaScript file, `allegato_708.js`, from the external IP address `62.173.142.148`. The JavaScript file was designed to execute through Windows Script Host using `wscript.exe`.

After execution, the script downloaded a second-stage DLL payload, `resources.dll`, from the domain `soundata.top`. This behavior is consistent with a staged malware infection where an initial script-based dropper retrieves and executes a more capable payload.

The incident demonstrates the importance of monitoring outbound HTTP requests, inspecting downloaded script files, and detecting suspicious use of Windows-native scripting utilities such as `wscript.exe` and `rundll32.exe`.

## Recommended Defensive Actions

1. Block the identified attacker IP and domain at the firewall, proxy, and DNS layers.
2. Search endpoint telemetry for execution of `wscript.exe` followed by network activity.
3. Hunt for the hashes of `allegato_708.js` and `resources.dll` across the environment.
4. Review proxy and DNS logs for access to `soundata.top`.
5. Restrict or monitor Windows Script Host execution where business use is not required.
6. Enable detection rules for script-based downloaders and DLL execution via `rundll32.exe`.

## Appendix: Answers Identified During the Lab

| Question | Answer |
|---|---|
| Which IP address was used by the attacker during the initial access? | `62.173.142.148` |
| What is the name of the malicious file used for initial access? | `allegato_708.js` |
| What is the SHA-256 hash of the malicious file used for initial access? | `847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476c4268` |
| Which process was used to execute the malicious file? | `wscript.exe` |
| What is the file extension of the second malicious file utilized by the attacker? | `.dll` |
| What is the MD5 hash of the second malicious file? | `e758e07113016aca55d9eda2b0ffeebe` |
