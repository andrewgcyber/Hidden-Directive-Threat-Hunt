# Technical Findings

---

# Overview

The investigation identified multiple indicators of malicious activity across endpoint telemetry, recovered malware artifacts, and static analysis. Evidence collected from Microsoft Defender XDR, Microsoft Sentinel, and PowerShell analysis was correlated to validate each finding and determine its significance.

The findings presented below are organized by category and supported by the evidence collected throughout the investigation.

---

# Finding 1 – Malicious PowerShell Execution

## Description

Microsoft Defender XDR identified suspicious PowerShell execution originating from **cmd.exe** on the compromised workstation (**GF-WS01**).

The observed command was:

```powershell
cmd /C powershell -ExecutionPolicy Unrestricted -File script49.ps1
```

Execution of PowerShell with the **Unrestricted** execution policy bypassed normal script execution restrictions, allowing the malicious script to run without interference.

## Evidence

- DeviceProcessEvents
- Process Command Line
- Parent-child process relationship

> **Figure 13.** DeviceProcessEvents confirming execution of `script49.ps1`.

```markdown
![Figure 13](../images/figure13-script49.png)

**Figure 13.** Microsoft Defender XDR telemetry showing `cmd.exe` launching `powershell.exe` with `script49.ps1`.
```

## Significance

PowerShell served as the initial execution mechanism observed during the investigation and enabled subsequent malware activity.

---

# Finding 2 – AMSI Bypass

## Description

Static analysis of the recovered PowerShell loader (`loader.ps1`) identified code implementing an Antimalware Scan Interface (AMSI) bypass.

By disabling AMSI inspection, the malware attempted to avoid detection by Microsoft Defender during execution.

## Evidence

- Static malware analysis
- loader.ps1

## Significance

AMSI bypass techniques are commonly used by fileless malware to evade endpoint detection.

---

# Finding 3 – Encoded Shellcode Payload

## Description

Analysis of the PowerShell loader confirmed that an XOR-encoded payload was downloaded and decoded before execution.

The recovered payload (`shellcode_encoded.bin`) was preserved and verified using SHA-256 hashing.

| Property | Value |
|----------|-------|
| File | shellcode_encoded.bin |
| SHA-256 | 523F4C317E03CD1AC811FA7E1C308EFD6DF1E2A61048C1C0BDD5B4D5FFB73C34 |

> **Figure 14.** SHA-256 verification of the recovered payload.

<img width="1099" height="233" alt="Screenshot 2026-07-31 172958" src="https://github.com/user-attachments/assets/bab0cb57-6b44-4c07-a014-8ecc30040a79" />


## Significance

Hashing preserved the integrity of the recovered artifact and allowed it to be documented as an Indicator of Compromise (IOC).

---

# Finding 4 – In-Memory Shellcode Execution

## Description

Static analysis identified multiple Windows API functions responsible for allocating executable memory and running decoded shellcode directly in memory.

Observed functionality included:

- VirtualAlloc
- CreateThread
- Memory protection changes

## Evidence

- loader.ps1
- shellcode analysis

## Significance

Executing shellcode directly in memory reduces the attacker's reliance on writing executable files to disk, making the malware more difficult to detect using traditional signature-based defenses.

---

# Finding 5 – Command and Control Communications

## Description

Network telemetry confirmed outbound HTTPS communication between the compromised workstation and attacker-controlled infrastructure.

Static analysis indicated that these connections were used to retrieve the encoded payload during execution.

## Evidence

- DeviceNetworkEvents
- Static malware analysis

## Significance

Outbound HTTPS communication demonstrated that the malware successfully contacted external infrastructure during execution.

---

# Finding 6 – Administrative Commands

## Description

Following PowerShell execution, Microsoft Defender XDR identified execution of administrative Windows utilities including **net.exe** and **net1.exe**.

Observed command examples included account management operations executed under the SYSTEM context.

> **Figure 15.** Administrative account management commands identified during the investigation.

```markdown
![Figure 15](../images/figure15-netexe.png)

**Figure 15.** DeviceProcessEvents showing execution of administrative commands after PowerShell activity.
```

## Significance

Although these utilities are legitimate Windows tools, their execution immediately following the malicious PowerShell activity increased their investigative significance and warranted further review.

The investigation did not identify sufficient evidence to conclude that administrative commands resulted in successful privilege escalation or unauthorized account creation.

---

# Finding 7 – Windows Management Instrumentation (WMI)

## Description

Microsoft Defender XDR identified Windows Management Instrumentation (WMI) activity during the investigation window.

WMI is commonly used for remote administration and system management but may also be leveraged during post-exploitation.

## Evidence

- Defender telemetry
- Process activity

## Significance

Observed WMI activity supported the conclusion that additional post-exploitation techniques were attempted following initial code execution.

No evidence confirmed successful lateral movement through WMI.

---

# Finding 8 – Persistence Assessment

## Description

Multiple persistence mechanisms were investigated, including:

- Scheduled Tasks
- Registry Run Keys
- Startup Folder
- Services

## Evidence

- DeviceRegistryEvents
- File telemetry
- Process telemetry

## Findings

No evidence of persistence mechanisms was identified during the investigation.

---

# Finding 9 – Credential Theft Assessment

## Description

The investigation reviewed endpoint telemetry for indicators of credential theft, including KeePass-related activity and administrative commands.

## Findings

Although KeePass activity was observed, the investigation did not identify evidence confirming successful credential theft or credential dumping.

---

# Finding 10 – Overall Impact Assessment

## Confirmed

- Malicious PowerShell execution
- Execution of script49.ps1
- AMSI bypass
- XOR-encoded shellcode
- In-memory execution
- HTTPS command-and-control communication
- Administrative command execution

## Not Confirmed

- Persistence
- Credential theft
- Lateral movement
- Data exfiltration

---

# Technical Findings Summary

The investigation confirmed a successful endpoint compromise involving malicious PowerShell execution, in-memory malware techniques, and outbound command-and-control communications.

Correlation of Microsoft Defender XDR telemetry with static malware analysis provided high confidence in the reconstructed attack chain while also demonstrating that several commonly observed post-exploitation objectives—including persistence, credential theft, and data exfiltration—were not supported by the available evidence.
