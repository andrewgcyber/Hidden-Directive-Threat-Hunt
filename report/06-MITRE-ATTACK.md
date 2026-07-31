# MITRE ATT&CK Mapping

---

# Overview

Observed attacker behavior was mapped to the MITRE ATT&CK® Enterprise Framework to classify the techniques identified during the investigation. Only techniques directly supported by Microsoft Defender XDR telemetry, Microsoft Sentinel correlation, and static malware analysis were included.

The mappings below are based on confirmed evidence collected throughout the investigation and are intended to provide standardized classification of the observed attack chain.

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ID | Evidence |
|---------|-----------|----|----------|
| Execution | PowerShell | T1059.001 | `script49.ps1` executed using `powershell.exe` |
| Defense Evasion | Impair Defenses | T1562.001 | AMSI bypass identified in `loader.ps1` |
| Defense Evasion | Obfuscated Files or Information | T1027 | XOR-encoded shellcode recovered |
| Command and Control | Application Layer Protocol: Web Protocols | T1071.001 | HTTPS communication observed in `DeviceNetworkEvents` |
| Discovery | Windows Management Instrumentation | T1047 | WMI activity identified during investigation |

---

# Technique Analysis

## T1059.001 – PowerShell

### Evidence

Microsoft Defender XDR confirmed execution of:

```powershell
cmd /C powershell -ExecutionPolicy Unrestricted -File script49.ps1
```

This command initiated the observed attack chain and executed the malicious PowerShell script responsible for loading the next-stage payload.

**Supporting Evidence**

- DeviceProcessEvents
- Process Command Line
- Parent-child process relationship

---

## T1562.001 – Impair Defenses

### Evidence

Static analysis of `loader.ps1` identified an Antimalware Scan Interface (AMSI) bypass.

The malware modified PowerShell behavior to reduce the likelihood of Microsoft Defender inspecting the malicious script before execution.

**Supporting Evidence**

- loader.ps1 analysis
- Static malware analysis

---

## T1027 – Obfuscated Files or Information

### Evidence

The PowerShell loader downloaded an XOR-encoded payload (`shellcode_encoded.bin`) before decoding it in memory.

This encoding technique was used to conceal the payload from basic inspection and delay detection until runtime.

**Supporting Evidence**

- loader.ps1
- shellcode_encoded.bin
- PowerShell analysis

---

## T1071.001 – Web Protocols

### Evidence

Microsoft Defender XDR DeviceNetworkEvents confirmed outbound HTTPS communications between the compromised workstation and attacker-controlled infrastructure.

Observed domains included:

- `cdn.cloud-endpoint.net`
- `api.cloud-endpoint.net`

These communications aligned with the payload retrieval functionality identified during static malware analysis.

**Supporting Evidence**

- DeviceNetworkEvents
- loader.ps1

---

## T1047 – Windows Management Instrumentation

### Evidence

Microsoft Defender XDR identified Windows Management Instrumentation (WMI) activity during the investigation window.

Although WMI is commonly used for legitimate administrative purposes, its presence following malicious PowerShell execution warranted additional investigation.

The available evidence did not confirm that WMI activity resulted in lateral movement.

---

# Techniques Investigated but Not Confirmed

Several ATT&CK techniques were investigated but were **not mapped** due to insufficient evidence.

| Technique | Reason Not Mapped |
|------------|------------------|
| Credential Dumping | No evidence of LSASS access or dumping tools |
| Lateral Movement | No evidence of successful movement between hosts |
| Persistence | No scheduled tasks, services, or registry persistence identified |
| Data Exfiltration | No evidence of outbound data transfers |

Maintaining a distinction between **confirmed** and **unconfirmed** techniques helps ensure the investigation remains evidence-based and avoids overstating attacker capabilities.

---

# MITRE ATT&CK Summary

The investigation identified a focused attack chain centered on malicious PowerShell execution, defense evasion, in-memory payload execution, and outbound command-and-control communication.

No evidence supported persistence, credential dumping, lateral movement, or data exfiltration during the investigation window.

The ATT&CK mappings presented in this report are limited to techniques directly supported by the collected evidence.
