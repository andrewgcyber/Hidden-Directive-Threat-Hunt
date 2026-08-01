# Root Cause Analysis

---

# Overview

The objective of the root cause analysis was to identify the initial compromise vector, determine how the malicious payload was executed, and establish the sequence of events that led to the observed attacker activity on **GF-WS01**.

Evidence from Microsoft Defender XDR, Microsoft Sentinel, and static malware analysis was correlated to reconstruct the attack chain and validate each stage of the compromise.

---

# Initial Access

Static analysis identified a malicious HTML file (`blog_lure.html`) containing hidden prompt injection instructions designed to retrieve and execute a PowerShell payload. The recovered loader (`loader.ps1`) was responsible for downloading an XOR-encoded shellcode payload and executing it directly in memory.

Although the exact user interaction that initiated execution could not be confirmed from the available telemetry, analysis indicates that the malicious PowerShell loader served as the primary execution mechanism observed during the investigation.

> **Figure 5.** 
<img width="1142" height="434" alt="image" src="https://github.com/user-attachments/assets/55361922-3718-4fce-8eae-af5b19aa583e" />

---

# Malicious PowerShell Execution

Microsoft Defender XDR telemetry confirmed execution of PowerShell through **cmd.exe** using an unrestricted execution policy.

Observed command:

```powershell
cmd /C powershell -ExecutionPolicy Unrestricted -File script49.ps1
```

The use of the **Unrestricted** execution policy allowed the script to bypass the system's default PowerShell execution restrictions and execute successfully.

> **Figure 6.** 
<img width="1154" height="360" alt="image" src="https://github.com/user-attachments/assets/41282744-b1aa-42e8-b689-81fc02acd8f9" />


**Figure 6.** Microsoft Defender XDR process telemetry confirming execution of `script49.ps1`.
```

---

# Static Malware Analysis

Analysis of the recovered PowerShell loader (`loader.ps1`) identified several malicious capabilities commonly associated with fileless malware.

Observed functionality included:

- AMSI bypass
- Remote payload download
- XOR payload decoding
- Windows API memory allocation
- In-memory shellcode execution
- HTTPS communication with attacker infrastructure

These findings were consistent with the endpoint telemetry collected from Microsoft Defender XDR and supported the conclusion that the malware attempted to avoid detection while executing entirely in memory.

---

# Shellcode Artifact

The investigation recovered the encoded shellcode payload (`shellcode_encoded.bin`) from the analysis environment.

The artifact was preserved and hashed to maintain integrity throughout the investigation.

| Property | Value |
|----------|-------|
| File Name | shellcode_encoded.bin |
| SHA-256 | 523F4C317E03CD1AC811FA7E1C308EFD6DF1E2A61048C1C0BDD5B4D5FFB73C34 |

PowerShell verification confirmed the file hash remained consistent during analysis.

> **Figure 7.** SHA-256 hash verification of the recovered shellcode payload.

<img width="1166" height="257" alt="Screenshot 2026-07-31 172918" src="https://github.com/user-attachments/assets/acec9987-b24b-4350-a4aa-2213205764dc" />

---

# Post-Exploitation Activity

Microsoft Defender XDR identified additional activity following PowerShell execution.

Observed behavior included:

- Administrative account management commands using **net.exe**
- Windows Management Instrumentation (WMI) activity
- Outbound HTTPS communications
- KeePass-related activity

These actions indicate that the attacker attempted to perform post-exploitation tasks after achieving code execution.

However, the available telemetry did not confirm successful persistence, lateral movement, or credential theft.

> **Figure 8.** DeviceProcessEvents showing administrative commands executed after PowerShell activity.
<img width="1370" height="757" alt="image" src="https://github.com/user-attachments/assets/e2ac7acc-1b6d-485f-8252-6d329818eb83" />

---

# Root Cause Determination

Based on the evidence collected throughout the investigation, the root cause was determined to be the execution of a malicious PowerShell loader that downloaded and executed an encoded shellcode payload entirely in memory.

The attack leveraged PowerShell with an unrestricted execution policy, implemented defense evasion through an AMSI bypass, and established outbound communications with attacker-controlled infrastructure.

Although several post-exploitation techniques were observed, the investigation did not identify sufficient evidence to confirm:

- Persistence
- Credential theft
- Lateral movement
- Data exfiltration

The available evidence indicates that the attacker successfully achieved code execution on **GF-WS01**, but the overall impact remained limited to the activity observed during the investigation window.

---

# Transition

After establishing the root cause of the compromise, the investigation focused on reconstructing the complete sequence of attacker actions. The following section presents a chronological timeline of the incident using endpoint telemetry, malware analysis, and correlated security events.
