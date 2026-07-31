# Indicators of Compromise (IOCs)

---

# Overview

Throughout the investigation, multiple Indicators of Compromise (IOCs) were identified through Microsoft Defender XDR telemetry, Microsoft Sentinel correlation, and static malware analysis. These indicators can be used by security teams to detect similar activity, develop detection rules, and support future threat hunting efforts.

The IOCs below represent artifacts directly observed or recovered during the investigation.

---

# Network Indicators

## Domains

| Domain | Purpose | Evidence |
|----------|---------|----------|
| `cdn.cloud-endpoint.net` | Payload retrieval / Command and Control | DeviceNetworkEvents |
| `api.cloud-endpoint.net` | Secondary attacker infrastructure | Static Malware Analysis |

These domains were referenced during malware execution and correlated with outbound HTTPS communication observed from the compromised endpoint.

---

# File Indicators

| File Name | Description |
|------------|-------------|
| `blog_lure.html` | Malicious HTML lure containing hidden prompt injection instructions |
| `script49.ps1` | Initial PowerShell execution script |
| `loader.ps1` | Malicious PowerShell loader responsible for downloading and executing the payload |
| `shellcode_encoded.bin` | XOR-encoded shellcode payload recovered during analysis |

---

# File Hashes

## SHA-256

| Artifact | SHA-256 |
|-----------|----------|
| `shellcode_encoded.bin` | `523F4C317E03CD1AC811FA7E1C308EFD6DF1E2A61048C1C0BDD5B4D5FFB73C34` |

The SHA-256 hash was calculated using PowerShell during the investigation to preserve artifact integrity and provide a reliable identifier for future detection efforts.

---

# Process Indicators

The following Windows processes were directly associated with the observed attack chain.

| Process | Description |
|----------|-------------|
| `cmd.exe` | Spawned the malicious PowerShell process |
| `powershell.exe` | Executed `script49.ps1` |
| `net.exe` | Administrative account management activity |
| `net1.exe` | Additional administrative utility execution |
| `WmiPrvSE.exe` | Windows Management Instrumentation activity |

Although several of these binaries are legitimate Windows utilities, their execution sequence and correlation with malicious PowerShell activity significantly increased their investigative relevance.

---

# Command-Line Indicators

The following PowerShell command was confirmed during the investigation:

```powershell
cmd /C powershell -ExecutionPolicy Unrestricted -File script49.ps1
```

This command initiated execution of the malicious PowerShell script and served as the starting point for the observed attack chain.

---

# PowerShell Characteristics

The recovered PowerShell loader demonstrated several notable characteristics that may be useful for future detection engineering.

Observed functionality included:

- AMSI bypass
- XOR payload decoding
- DownloadData()
- VirtualAlloc()
- Marshal.Copy()
- CreateThread()

These behaviors indicate an attempt to evade endpoint detection while executing malicious code entirely in memory.

---

# Detection Opportunities

Based on the observed indicators, organizations should consider monitoring for:

- PowerShell executed with `-ExecutionPolicy Unrestricted`
- PowerShell downloading remote content
- AMSI bypass techniques
- `VirtualAlloc()` followed by `CreateThread()`
- HTTPS communications to unknown infrastructure
- Administrative utilities (`net.exe`, `net1.exe`) executed immediately after PowerShell activity

---

# IOC Summary

The indicators identified during this investigation provide multiple opportunities for detection across endpoint, network, and security monitoring platforms.

Correlating these indicators with endpoint telemetry increases confidence when identifying similar attack patterns and supports proactive threat hunting across enterprise environments.
