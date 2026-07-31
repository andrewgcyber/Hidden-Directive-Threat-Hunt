# Greenfield Threat Hunt Investigation

> **Enterprise Malware Investigation using Microsoft Defender XDR, Microsoft Sentinel, and Static Malware Analysis**

![Banner](images/cover.png)

---

## Executive Summary

On **July 4, 2026**, Greenfield Logistics experienced a Priority 1 (P1) security incident after Microsoft Defender XDR detected suspicious PowerShell activity originating from workstation **GF-WS01**. The Managed Security Service Provider (MSSP) escalated the incident after identifying abnormal process execution involving the **sancadmin** account, prompting a full threat hunting investigation to determine the source of the compromise, identify affected systems, and assess the overall impact.

Using Microsoft Defender XDR, Microsoft Sentinel, Kusto Query Language (KQL), and static malware analysis, the investigation reconstructed the complete attack chain. Endpoint telemetry confirmed execution of an unrestricted PowerShell script, outbound HTTPS communications with attacker-controlled infrastructure, administrative command execution, and Windows Management Instrumentation (WMI) activity. Recovered malware artifacts further revealed that the attack relied on a staged PowerShell loader capable of bypassing Microsoft's Antimalware Scan Interface (AMSI), downloading an XOR-encoded payload, and executing shellcode directly in memory.

Although several post-exploitation techniques were observed throughout the investigation, no evidence confirmed successful persistence, credential theft, lateral movement, or data exfiltration during the investigation window. The findings indicate that the attack successfully achieved code execution and command-and-control communication, but the available evidence did not demonstrate a complete compromise of the Greenfield environment.

This repository documents the complete investigation, including the threat hunting methodology, timeline reconstruction, malware analysis, indicators of compromise (IOCs), MITRE ATT&CK mapping, and remediation recommendations.

---

## Investigation Objectives

The primary objectives of this investigation were to:

- Identify the initial compromise vector.
- Determine the scope of affected hosts and user accounts.
- Reconstruct the complete attack timeline using Microsoft Defender XDR and Microsoft Sentinel telemetry.
- Perform static analysis of recovered malware artifacts.
- Identify attacker techniques and map them to the MITRE ATT&CK framework.
- Assess the overall impact of the incident.
- Develop evidence-based remediation recommendations.

---

## Environment

| Component | Details |
|-----------|---------|
| Organization | Greenfield Logistics |
| Investigation Type | Threat Hunt / Incident Response |
| Primary Platform | Microsoft Defender XDR |
| Secondary Platform | Microsoft Sentinel |
| Primary Host | GF-WS01 |
| Additional Hosts | GF-SRV01, GF-DC01 |
| Primary User | sancadmin |
| Investigation Window | July 4–5, 2026 |

---

## Tools Used

- Microsoft Defender XDR
- Microsoft Sentinel
- Microsoft Defender Advanced Hunting
- Kusto Query Language (KQL)
- Windows 11 Analysis Virtual Machine
- PowerShell
- Static Malware Analysis

---

## Investigation Methodology

The investigation followed a structured threat hunting methodology to validate the incident, reconstruct attacker activity, and correlate endpoint telemetry with recovered malware artifacts.

The investigation consisted of the following phases:

1. Confirm the investigation scope.
2. Validate affected systems.
3. Collect endpoint telemetry.
4. Reconstruct process execution.
5. Analyze network communications.
6. Perform static malware analysis.
7. Correlate evidence.
8. Produce an incident report.

---

## Investigation Scope

The investigation focused on the three systems identified within the original incident briefing.

| Host | Purpose |
|------|---------|
| **GF-WS01** | Primary compromised workstation |
| **GF-SRV01** | Investigated for lateral movement |
| **GF-DC01** | Investigated for authentication activity |

The investigation window was limited to **July 4, 2026 through July 5, 2026** in order to isolate attacker activity from normal administrative operations.

---

## Investigation Workflow

The following workflow was used throughout the investigation.

```text
Incident Brief
        │
        ▼
Environment Validation
        │
        ▼
Microsoft Defender XDR Hunting
        │
        ▼
Microsoft Sentinel Correlation
        │
        ▼
Timeline Reconstruction
        │
        ▼
Static Malware Analysis
        │
        ▼
Root Cause Analysis
        │
        ▼
IOC Development
        │
        ▼
MITRE ATT&CK Mapping
        │
        ▼
Incident Report
```

---

## Initial Investigation

The investigation began by reviewing the incident brief provided by Greenfield Logistics. The briefing identified **GF-WS01** as the primary workstation involved in the incident and instructed investigators to determine the source of the compromise using Microsoft Defender XDR and Microsoft Sentinel telemetry.

The first phase of the investigation focused on validating the environment, confirming that all systems were actively reporting telemetry, and identifying the primary user account associated with the incident.

![Figure 1](images/figure01-brief.png)

**Figure 1.** Initial incident briefing outlining the investigation objectives, affected systems, and required data sources.

Following the initial briefing, Microsoft Defender XDR Advanced Hunting was used to confirm the investigation scope and identify the hosts included in the threat hunt.

![Figure 2](images/figure02-deviceinfo.png)

**Figure 2.** Microsoft Defender XDR environment validation confirming the systems included within the investigation scope.

---

➡️ **Continue to Part 2: Root Cause Analysis and Timeline**
---

# Root Cause Analysis

## Executive Overview

The investigation determined that the incident originated from the execution of a malicious PowerShell script on **GF-WS01**. Endpoint telemetry collected from Microsoft Defender XDR, combined with static analysis of the recovered malware artifacts, confirmed that the attack leveraged PowerShell to execute malicious code, retrieve a secondary payload, and establish outbound communication with attacker-controlled infrastructure.

Unlike traditional malware that relies on dropping executable files to disk, this attack used a staged PowerShell loader capable of disabling Microsoft's Antimalware Scan Interface (AMSI), downloading an XOR-encoded payload, allocating executable memory, and launching shellcode directly in memory. This execution method significantly reduces file-based indicators while allowing malicious code to run within trusted Windows processes.

Although additional administrative activity involving **net.exe**, **KeePass**, and **Windows Management Instrumentation (WMI)** was observed, the investigation did not identify sufficient evidence to confirm persistence, successful credential theft, lateral movement, or data exfiltration.

Based on the available evidence, the root cause of the incident was determined to be the successful execution of a malicious PowerShell loader that enabled in-memory malware execution and command-and-control communication.

---

## Attack Chain

The following diagram summarizes the observed attack chain reconstructed during the investigation.

```text
User Interaction
        │
        ▼
PowerShell Execution
(script49.ps1)
        │
        ▼
PowerShell Loader
(loader.ps1)
        │
        ▼
AMSI Bypass
        │
        ▼
Download Encoded Payload
(shellcode_encoded.bin)
        │
        ▼
XOR Decode
        │
        ▼
VirtualAlloc()
        │
        ▼
CreateThread()
        │
        ▼
HTTPS Command & Control
        │
        ▼
Post-Exploitation Activity
```

---

## Root Cause

The primary cause of the incident was the execution of **script49.ps1** using **PowerShell** with the **ExecutionPolicy Unrestricted** parameter.

This execution allowed the attacker to bypass normal PowerShell execution policy restrictions and launch a staged PowerShell loader responsible for downloading and executing a secondary payload.

Static analysis of the recovered loader identified several techniques commonly associated with fileless malware, including:

- AMSI bypass
- Remote payload retrieval
- XOR payload decoding
- In-memory shellcode execution
- Windows API memory allocation
- HTTPS command-and-control communication

These techniques collectively demonstrate an attack designed to minimize disk artifacts while executing malicious code directly in memory.

---

## Root Cause Evidence

### Evidence 1 – Initial PowerShell Execution

Process creation telemetry identified **cmd.exe** spawning **powershell.exe** using the following command:

```powershell
cmd /C powershell -ExecutionPolicy Unrestricted -File script49.ps1
```

This event marked the beginning of the observed attack chain and established the initial execution of the malicious PowerShell script.

![Figure 3](images/figure03-powershell-launch.png)

**Figure 3.** Microsoft Defender XDR `DeviceProcessEvents` showing `cmd.exe` launching `powershell.exe` with `-ExecutionPolicy Unrestricted -File script49.ps1`.

---

### Evidence 2 – Administrative Activity

Shortly after PowerShell execution, Microsoft Defender XDR identified execution of **net.exe** performing password-related administrative commands involving the **sancadmin** account.

Although these commands demonstrate administrative activity following malware execution, additional telemetry did not confirm successful credential compromise.

![Figure 4](images/figure04-netexe.png)

**Figure 4.** Device process timeline showing execution of `net.exe` during the investigation.

---

### Evidence 3 – Static Analysis of loader.ps1

Static analysis performed inside an isolated Windows virtual machine identified several malicious capabilities within **loader.ps1**, including:

| Capability | Purpose |
|------------|---------|
| AMSI Bypass | Disable PowerShell scanning |
| DownloadData() | Retrieve remote payload |
| XOR Decode | Deobfuscate payload |
| VirtualAlloc() | Allocate executable memory |
| Marshal.Copy() | Copy shellcode into memory |
| CreateThread() | Execute shellcode |
| WaitForSingleObject() | Maintain payload execution |

Rather than writing an executable to disk, the loader executed the decoded payload directly from memory, reducing opportunities for traditional antivirus detection.

![Figure 5](images/figure05-loader.png)

**Figure 5.** Static analysis of `loader.ps1` highlighting AMSI bypass, payload download, and in-memory execution routines.

---

### Evidence 4 – Command and Control Communication

Microsoft Defender XDR `DeviceNetworkEvents` confirmed outbound HTTPS communication initiated by **powershell.exe** to the attacker-controlled domain:

```
cdn.cloud-endpoint.net
```

This communication directly correlates with the network retrieval functionality identified during static analysis of **loader.ps1**, providing strong evidence that the recovered script and observed endpoint telemetry describe the same attack chain.

![Figure 6](images/figure06-network.png)

**Figure 6.** Outbound HTTPS communication from `powershell.exe` to attacker infrastructure.

---

## Root Cause Summary

The investigation concluded with **high confidence** that the compromise resulted from execution of a malicious PowerShell loader. Endpoint telemetry, command-line analysis, network communications, and static malware analysis consistently supported the same attack sequence:

1. Execution of `script49.ps1`
2. Launch of `loader.ps1`
3. AMSI bypass
4. Download of an XOR-encoded payload
5. In-memory shellcode execution
6. Outbound HTTPS communication to attacker infrastructure

Although additional post-exploitation activity was observed, no evidence confirmed persistence, successful credential theft, or data exfiltration during the investigation window.

> [!IMPORTANT]
> **Key Takeaway:** The investigation established that the attacker successfully achieved code execution and command-and-control communication through a fileless PowerShell attack. However, the available evidence did not demonstrate that the attacker maintained persistence or successfully compromised credentials during the observed timeframe.

---

# Attack Timeline

The following timeline reconstructs the attack using Microsoft Defender XDR telemetry, Microsoft Sentinel, and static malware analysis. Each event was validated using endpoint telemetry and correlated with the recovered malware artifacts.

| Time (UTC) | Event | Evidence |
|------------|-------|----------|
| 10:00 | Investigation window begins | Defender XDR |
| 10:01 | `cmd.exe` launches `powershell.exe` using `-ExecutionPolicy Unrestricted -File script49.ps1` | Figure 3 |
| 10:02 | PowerShell executes under the `sancadmin` account | DeviceProcessEvents |
| 10:02 | `net.exe` performs password-related administrative commands | Figure 4 |
| 10:03 | Outbound HTTPS communication observed | Figure 6 |
| 10:04 | WMI activity identified | WmiPrvSE.exe |
| Investigation | Static analysis confirms AMSI bypass and shellcode execution | Figure 5 |
| Investigation | Shellcode hash documented and preserved | `shellcode_encoded.bin` |

> [!NOTE]
> The timeline above combines endpoint telemetry with recovered malware artifacts to provide a complete view of the attack sequence.
