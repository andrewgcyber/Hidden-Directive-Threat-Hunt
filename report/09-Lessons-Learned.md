# Lessons Learned

---

# Overview

The Greenfield Logistics threat hunting investigation demonstrated the value of combining endpoint telemetry, centralized log analysis, and static malware analysis to reconstruct a complex attack chain. By correlating Microsoft Defender XDR telemetry with Microsoft Sentinel events and recovered malware artifacts, the investigation established a high-confidence understanding of the attack while ensuring that conclusions remained evidence-based.

Throughout the investigation, emphasis was placed on validating findings through multiple independent sources rather than relying on a single artifact or alert. This approach reduced the likelihood of false positives and provided greater confidence in the final assessment.

---

# Investigation Successes

The investigation successfully achieved its primary objectives.

### Confirmed Findings

- Successfully reconstructed the attack timeline.
- Confirmed malicious PowerShell execution.
- Identified the execution of `script49.ps1`.
- Recovered and analyzed the malicious PowerShell loader (`loader.ps1`).
- Identified an AMSI bypass implemented within the loader.
- Recovered and documented the XOR-encoded shellcode payload.
- Verified outbound HTTPS communications with attacker-controlled infrastructure.
- Documented Windows Management Instrumentation (WMI) activity.
- Produced Indicators of Compromise (IOCs).
- Mapped confirmed techniques to the MITRE ATT&CK framework.

These findings provided sufficient evidence to explain how the compromise occurred and how the attacker attempted to evade detection.

---

# Investigation Limitations

Although the investigation successfully reconstructed the observed attack chain, several activities could not be confirmed based on the available evidence.

The investigation did **not** identify evidence confirming:

- Persistence mechanisms
- Successful credential theft
- Credential dumping
- Lateral movement
- Data exfiltration

Rather than assuming these activities occurred, the investigation documented them as **not confirmed**. Maintaining this distinction ensures that conclusions remain objective and supported by evidence.

---

# Key Technical Takeaways

Several important observations were made throughout the investigation.

### PowerShell Remains a High-Risk Attack Vector

The attack demonstrated how legitimate Windows utilities can be abused to execute malicious code. PowerShell provided the attacker with a flexible execution mechanism while reducing reliance on traditional malware binaries.

---

### Fileless Malware Presents Detection Challenges

The recovered PowerShell loader executed shellcode directly in memory after downloading an encoded payload. This technique reduced disk artifacts and highlighted the importance of behavioral detection over signature-based detection.

---

### Correlating Multiple Evidence Sources Improves Confidence

No single artifact explained the entire attack.

Instead, the investigation relied on:

- Microsoft Defender XDR endpoint telemetry
- Microsoft Sentinel event correlation
- Static malware analysis
- PowerShell artifact analysis
- Timeline reconstruction

Together, these evidence sources produced a complete understanding of the incident.

---

# Professional Skills Demonstrated

This investigation demonstrates practical experience in:

- Threat Hunting
- Incident Response
- Microsoft Defender XDR
- Microsoft Sentinel
- Kusto Query Language (KQL)
- Static Malware Analysis
- MITRE ATT&CK Mapping
- IOC Development
- Timeline Reconstruction
- Digital Forensics Documentation
- Technical Report Writing

These skills reflect the workflow commonly used by Security Operations Center (SOC) analysts and Digital Forensics & Incident Response (DFIR) professionals during enterprise investigations.

---

# Final Assessment

The investigation concluded with **high confidence** that the observed activity originated from a malicious PowerShell loader executed on **GF-WS01**. The loader successfully bypassed AMSI protections, retrieved an XOR-encoded payload, executed shellcode directly in memory, and established outbound HTTPS communications with attacker-controlled infrastructure.

Although several post-exploitation techniques were observed, the available evidence did not confirm persistence, credential theft, lateral movement, or data exfiltration during the investigation window.

By combining Microsoft Defender XDR telemetry, Microsoft Sentinel correlation, and static malware analysis, the investigation successfully reconstructed the attack chain and produced evidence-based findings that can support future threat hunting, detection engineering, and incident response efforts.

---

# Closing Statement

This project demonstrates a complete threat hunting workflow, from initial alert triage through malware analysis, timeline reconstruction, ATT&CK mapping, IOC development, and remediation planning. The investigation highlights the importance of evidence correlation, analytical reasoning, and clear technical communication when responding to enterprise security incidents.

The techniques and methodology documented throughout this repository provide a repeatable approach for investigating similar PowerShell-based attacks in enterprise environments.
