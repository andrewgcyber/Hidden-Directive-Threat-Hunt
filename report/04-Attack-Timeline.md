# Attack Timeline

---

# Overview

The attack timeline was reconstructed by correlating endpoint telemetry from Microsoft Defender XDR with static malware analysis findings. Events were organized chronologically to establish the progression of attacker activity from initial execution through post-exploitation.

The timeline below includes only activity directly supported by evidence collected during the investigation.

---

# Timeline of Events

| Time (UTC) | Event | Evidence |
|------------|-------|----------|
| July 4, 2026 | Microsoft Defender XDR detected suspicious activity on **GF-WS01** | Defender Alert |
| 06:01:34 | `cmd.exe` launched `powershell.exe` using `-ExecutionPolicy Unrestricted -File script49.ps1` | DeviceProcessEvents |
| 06:01:35 | PowerShell execution confirmed | DeviceProcessEvents |
| Shortly After | Administrative account management commands executed using `net.exe` | DeviceProcessEvents |
| Shortly After | Additional `net1.exe` activity observed | DeviceProcessEvents |
| During Execution | Loader downloaded and decoded an XOR-encoded payload | Static Malware Analysis |
| During Execution | Shellcode executed directly in memory | Static Malware Analysis |
| During Execution | HTTPS communication established with attacker-controlled infrastructure | DeviceNetworkEvents |
| During Investigation | Encoded shellcode recovered and hashed | PowerShell Analysis |
| Investigation Complete | No confirmed persistence, credential theft, lateral movement, or data exfiltration identified | Correlated Investigation Findings |

---

# Phase 1 – Initial Execution

The earliest confirmed malicious activity occurred when Microsoft Defender XDR recorded **cmd.exe** launching **powershell.exe** using an unrestricted execution policy.

Observed command:

```powershell
cmd /C powershell -ExecutionPolicy Unrestricted -File script49.ps1
```

This event marked the beginning of the observed attack chain and established PowerShell as the primary execution mechanism used by the attacker.

---

> **Figure 9.** DeviceProcessEvents showing execution of `script49.ps1`.
<img width="1202" height="704" alt="image" src="https://github.com/user-attachments/assets/508b8cf9-424d-44e6-9e6f-015c5478f153" />

**Figure 9.** Microsoft Defender XDR process telemetry confirming execution of the malicious PowerShell script.
```

---

# Phase 2 – PowerShell Activity

Following execution of `script49.ps1`, Microsoft Defender XDR recorded additional PowerShell activity initiated from the original process.

Process telemetry confirmed the parent-child relationship between `cmd.exe` and `powershell.exe`, validating the execution sequence identified during malware analysis.

PowerShell activity aligned with the functionality observed within the recovered loader (`loader.ps1`).

---

> **Figure 10.** PowerShell process execution identified in Microsoft Defender XDR.

```markdown
![Figure 10](../images/figure10-powershell-process.png)

**Figure 10.** Additional PowerShell execution observed after initial script launch.
```

---

# Phase 3 – Administrative Commands

Immediately following PowerShell execution, endpoint telemetry identified administrative account management activity involving **net.exe** and **net1.exe**.

Observed commands included administrative user management operations executed under the SYSTEM context.

Although these commands are legitimate Windows utilities, their proximity to the malicious PowerShell execution increased their investigative significance.

---

> **Figure 11.** Administrative commands executed after PowerShell activity.

```markdown
![Figure 11](../images/figure11-netexe.png)

**Figure 11.** DeviceProcessEvents showing execution of `net.exe` following PowerShell activity.
```

---

# Phase 4 – Malware Execution

Static analysis of the recovered loader confirmed that execution continued through several stages designed to evade detection.

Observed functionality included:

- AMSI bypass
- XOR payload decoding
- Shellcode execution in memory
- Windows API memory allocation
- HTTPS communication

These findings aligned closely with the endpoint telemetry collected from Microsoft Defender XDR.

---

# Phase 5 – Command and Control

Network telemetry confirmed outbound HTTPS communications between the compromised workstation and attacker-controlled infrastructure.

These communications supported the conclusion that the malware successfully established external communications following execution.

No evidence suggested large-scale outbound transfers consistent with data exfiltration.

---

# Phase 6 – Investigation Validation

During the investigation, the encoded payload (`shellcode_encoded.bin`) was recovered from the analysis environment.

PowerShell was used to:

- Calculate the SHA-256 hash
- Verify file integrity
- Document artifact metadata

These steps ensured that the recovered evidence could be reliably referenced throughout the investigation.

---

> **Figure 12.** SHA-256 verification of the recovered payload.
<img width="1099" height="233" alt="Screenshot 2026-07-31 172958" src="https://github.com/user-attachments/assets/12985a95-6fff-4c2f-88f9-1a2e11acff23" />

---

# Timeline Summary

Correlating endpoint telemetry with malware analysis allowed the complete attack sequence to be reconstructed with a high degree of confidence.

The investigation confirmed:

- Malicious PowerShell execution.
- Execution of `script49.ps1`.
- Administrative command execution.
- In-memory shellcode execution.
- HTTPS command-and-control communications.

Although several post-exploitation techniques were observed, the available telemetry did not confirm persistence, credential theft, lateral movement, or data exfiltration during the investigation window.

---

# Transition

After reconstructing the attack timeline, the investigation shifted to a detailed examination of each technical finding. The following section analyzes the evidence collected from endpoint telemetry, malware artifacts, and network activity to explain how each finding contributed to the overall investigation.
