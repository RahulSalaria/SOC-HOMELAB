# Host-Based Threat Emulation & SOC Detection Engineering Lab

## Executive Summary
This project demonstrates the design and validation of an enterprise log forwarding pipeline used to monitor and detect malicious endpoint activity. By simulating "Living off the Land" (LotL) tactics directly on a local production host (`target-PC`), I identified an inherent Windows logging blind spot. I remediated this gap by engineering custom OS-level process auditing policies and verified the resulting behavioral Indicators of Compromise (IoCs) within a centralized Splunk dashboard.

## Lab Architecture & Topology
- **Victim & Emulation Endpoint:** Windows Workstation (`target-PC`) running the Wazuh Endpoint Agent.
- **SIEM & Data Analytics Stack:** Ubuntu Infrastructure hosting Wazuh Server and a central Splunk dashboard.

---

## Phase 1: Local Threat Emulation
To simulate an adversary acting inside the host, I executed two distinct threat techniques directly within the Windows command terminal:

1. **Deobfuscation & Evasion (MITRE ATT&CK T1027):** Used the native Microsoft utility `certutil.exe` to decode a local mock-encoded file offline:
   `certutil.exe -decode "$env:TEMP\encoded.txt" "$env:TEMP\legal_file.exe"`
   
2. **Registry Run Keys Persistence (MITRE ATT&CK T1547.001):** Utilized the standard command-line registry tool to inject an automatic startup shortcut named `LegitUpdater`, ensuring the mock payload executes every time the user logs in:
   `reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v "LegitUpdater" /t REG_SZ /d "%temp%\legal_file.exe" /f`

---

## Phase 2: Telemetry Engineering & Log Hunting
### 1. Remediating the Visibility Blind Spot
Initial testing showed that default Windows security logs hid explicit command line arguments, returning 0 search results in Splunk. To gain full visibility, I opened an elevated Administrator command prompt on `target-PC` and enforced advanced process creation auditing:
```cmd
auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable
reg add "hklm\software\microsoft\windows\currentversion\policies\system\audit" /v "ProcessCreationIncludeCmdLine_Enabled" /t REG_DWORD /d 1 /f
```

### 2. Splunk Log Verification
Once the host visibility rules were active, I re-ran the emulation steps. The telemetry successfully streamed across the network from the Windows agent into Splunk. Using the targeted Search Processing Language (SPL) query below, I cleanly isolated our attack footprints:

`index=* "certutil"`

The Splunk log window successfully tracked the event, returning **exactly 4 targeted events** that captured the execution indicators confirming the activity.
