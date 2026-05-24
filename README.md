# CyberDefenders-DanaBot-Lab

![Platform](https://img.shields.io/badge/Platform-CyberDefenders-blue)
![Category](https://img.shields.io/badge/Category-Network%20Forensics-orange)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Score](https://img.shields.io/badge/Score-100%25-brightgreen)
![Tools](https://img.shields.io/badge/Tools-Wireshark%20%7C%20VirusTotal%20%7C%20ANY.RUN-purple)

## Scenario
Analyze network traffic using Wireshark to identify DanaBot initial access, deobfuscate malicious JavaScript, and extract IOCs like IPs, file hashes, and execution processes.

## Tools Used
- Wireshark
- VirusTotal
- ANY.RUN
- Network Miner

## Investigation

### Q1 - Attacker IP address (initial access)
**Answer:** `62.173.142.148`

Filtered HTTP/suspicious traffic in Wireshark to identify the C2 server IP communicating with the victim during initial access.

---

### Q2 - Malicious file used for initial access
**Answer:** `allegato_708.js`

Extracted the file from the PCAP using Wireshark's `File > Export Objects > HTTP`. The `.js` extension indicates a malicious JavaScript dropper, commonly delivered via phishing emails as attachments.

---

### Q3 - SHA-256 hash of malicious file
**Answer:** `847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476`

Computed the hash after extracting the file, then verified on VirusTotal confirming it as a DanaBot dropper.

---

### Q4 - Process used to execute the malicious file
**Answer:** `wscript.exe`

`wscript.exe` (Windows Script Host) is the native Windows process for executing `.js` and `.vbs` files — a common living-off-the-land technique abused by attackers to run malicious scripts without triggering AV.

---

### Q5 - File extension of second malicious file
**Answer:** `.dll`

The JavaScript dropper downloaded a second-stage DLL payload. DLLs are commonly used in second-stage delivery to evade detection and execute via proxy binaries like `rundll32.exe`.

---

### Q6 - MD5 hash of second malicious file
**Answer:** `e758e07113016aca55d9eda2b0ffeebe`

Verified on VirusTotal — confirms the DLL as part of the DanaBot malware family.

---

## Attack Chain
```
Phishing email with allegato_708.js attachment
  → Victim executes JS file via wscript.exe
  → JS dropper connects to 62.173.142.148
  → Downloads second-stage .dll payload
     (MD5: e758e07113016aca55d9eda2b0ffeebe)
  → DanaBot establishes C2 communication
  → Credential theft & banking fraud
```

## IOC Summary
| Type | Value |
|------|-------|
| C2 IP | `62.173.142.148` |
| Initial file | `allegato_708.js` |
| SHA-256 | `847b4ad90b1daba2d9117a8e05776f3f902dda593fb1252289538acf476` |
| Execution process | `wscript.exe` |
| Second payload | `.dll` |
| MD5 | `e758e07113016aca55d9eda2b0ffeebe` |

## Key Takeaways
- JavaScript droppers delivered via phishing are a primary DanaBot delivery method
- `wscript.exe` is a native Windows binary abused for LotL execution
- Always extract and hash files from PCAPs and verify against VirusTotal
- Network forensics with Wireshark can reconstruct the full attack chain from traffic alone
