# CIP-B105-CS3_C11-26-DFIT-17300 - Windows 7 Memory Forensics - Illegal File Transfer

**Examiner:** Simon Friday Adeka  
**Evidence:** memdumpWin7.mem - Windows 7 Professional SP1 x86  
**Case:** CIP-B105-CS3_C11-26-DFIT-17300  
**Profile:** Win7SP1x86_23418  
**Result:** Illegal transfer PROVEN - 95% Very High Confidence  

![Forensics](https://img.shields.io/badge/Volatility-2.6.1-blue) ![Platform](https://img.shields.io/badge/Windows-7_Pro_SP1_7601-lightgrey) ![Status](https://img.shields.io/badge/Result-PROVEN-green)

## Executive Summary

Host **JEWIN7 / IE8WIN7** used by **IEUser [SID 1000]** to download `secret_file.docx` via Chrome and copy to removable **F:**.

**Chain:** Chrome Download Service EntryDB -> `\Users\IEUser\Downloads` -> `cd Downloads` -> `copy secret_file.docx F:` -> MountPoints2 `(S) F` + 5 GUIDs `2019-01-06 15:03:07 UTC`.

Secondary: OpenSSH backdoor `sshd.exe 0.0.0.0:22` LISTENING PID 2016 with AUTO_START persistence + explorer injection VAD 0x3250000 RWX.
### Full Report
📄 `Report/CIP-B105-CS3_C11-26-DFIT-17300_Simon_Friday_Adeka.pdf`

### Screenshots
All evidence in `docs/04_Screenshots/`:

- Fig01 Download 100%, Fig02 Hash SHA256/MD5, Fig03 ImageInfo
- Fig05 Hivelist, Fig08-10 ComputerName JEWIN7/IE8WIN7, Fig09 ProductName Win7Pro
- Fig12 TimeZone PST, Fig14 hashdump, Fig15 Run Keys, Fig16 pstree
- Fig18 netscan sshd 0.0.0.0:22 LISTENING, Fig19 malfind explorer injection
- Fig20 consoles `copy secret_file.docx F:` - 
- Fig22 filescan Chrome EntryDB, Fig23 MountPoints2 F: + GUIDs, Fig24 MountedDevices D: F:

### Verdict
Illegal transfer PROVEN. Chrome->Downloads->F: with 95% Very High Confidence.
