# CIP-B105-CS3_C11-26-DFIT-17300 - Windows 7 Memory Forensics

**Examiner:** Simon Friday Adeka | **Profile:** Win7SP1x86_23418 | **Result:** PROVEN 95%

### Executive Summary - GitHub Version
Host **JEWIN7 / IE8WIN7** used by **IEUser SID 1000** to download `secret_file.docx` via Chrome and copy to removable **F:**.

**Chain:** Chrome EntryDB -> `\Users\IEUser\Downloads` -> `cd Downloads` -> `copy secret_file.docx F:` -> MountPoints2 `F:` + 5 GUIDs `2019-01-06 15:03:07 UTC`.

### Full Report
📄 `Report/CIP-B105-CS3_C11-26-DFIT-17300_Simon_Friday_Adeka.pdf`

### Screenshots
All evidence in `docs/04_Screenshots/`:

- Fig01 Download 100%, Fig02 Hash SHA256/MD5, Fig03 ImageInfo
- Fig05 Hivelist, Fig08-10 ComputerName JEWIN7/IE8WIN7, Fig09 ProductName Win7Pro
- Fig12 TimeZone PST, Fig14 hashdump, Fig15 Run Keys, Fig16 pstree
- Fig18 netscan sshd 0.0.0.0:22 LISTENING, Fig19 malfind explorer injection
- Fig20 consoles `copy secret_file.docx F:` - SMOKING GUN
- Fig22 filescan Chrome EntryDB, Fig23 MountPoints2 F: + GUIDs, Fig24 MountedDevices D: F:

### Verdict
Illegal transfer PROVEN. Chrome->Downloads->F: with 95% Very High Confidence.
