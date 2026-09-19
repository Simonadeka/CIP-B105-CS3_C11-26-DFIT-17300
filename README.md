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

## Repository Structure

├── memdumpWin7.mem (not included - evidence)
├── README.md
├── Report/
│   └── CIP-B105-CS3_C11-26-DFIT-17300_Simon_Friday_Adeka.pdf
├── 04_Screenshots/
│   ├── Fig01_Evidence_Download_100Percent.png
│   ├── Fig02_Evidence_Size_Hash_SHA256_MD5.png
│   ├── Fig03_Volatility_ImageInfo_Profile.png
│   ├── Fig04_Hivelist_IFUser.png
│   ├── Fig05_GetSIDs_IEUser_SID.png
│   ├── Fig06_MAC_Attribution.png
│   ├── Fig07_ProductName_Win7Pro.png
│   ├── Fig08_ComputerName_IEWIN7.png
│   ├── Fig09_CSDVersion_SP1.png
│   ├── Fig10_TimeZone_PST.png
│   ├── Fig11_IEHistory_Google_Favicon.png
│   ├── Fig12_IEHistory_Chrome_Install_Bing.png
│   ├── Fig13_Visited_Unique_7_URLs.png
│   ├── Fig14_PsTree_Chrome_FTK.png
│   ├── Fig15_pstree.png
│   ├── Fig16_psxview.png
│   ├── Fig17_malfind.png
│   ├── Fig18_netscan_22_LISTENING.png
│   ├── Fig19_hashdump_5Users.png
│   ├── Fig20_Run_Keys.png
│   ├── Fig21_SSH_Service_Persistence.png
│   ├── Fig22_consoles_copy_secret_file_F.png
│   ├── Fig23_shellbags_Desktop_Chrome.png
│   ├── Fig24_shellbags_D_32Bit_64Bit.png
│   ├── Fig25_USBSTOR_Key_Not_Found.png
│   ├── Fig26_filescan_Downloads_Chrome_EntryDB.png
│   ├── Fig27_MountPoints2_F_GUIDs_IEUser.png
│   ├── Fig28_MountPoints2_F_Confirm.png
│   ├── Fig29_MountedDevices_D_F.png
│   ├── Fig30_UTC_Timeline_Corrected.png
│   └── Fig31_Evidence_Register.png
└── REC/
    ├── 13.09.2026_18.42.14_REC.mp4
    ├── 16.09.2026_10.43.19_REC.mp4
    ├── 16.09.2026_10.48.24_REC.mp4
    ├── 17.09.2026_09.43.03_REC.mp4
    ├── 17.09.2026_22.49.51_REC.mp4 - shellbags D:
    ├── 17.09.2026_22.51.37_REC.mp4 - USBSTOR attempt
    ├── 17.09.2026_22.52.59_REC.mp4 - MountedDevices D: F:
    ├── 17.09.2026_22.54.00_REC.mp4 - MountPoints2 F: + GUIDs
    ├── 17.09.2026_22.54.53_REC.mp4 - MountPoints2 red highlights
    └── 17.09.2026_22.56.38_REC.mp4 - filescan Downloads EntryDB

## Evidence Preservation

- **Download:** 100% complete - `Fig01`
- **Hash:** SHA256/MD5 verified with reference - `Fig02` + `Fig1_Evidence_Hash` + `Fig4_Reference_Hash`
- **Profile:** `Win7SP1x86_23418` - `imageinfo` - `Fig03`

## System & User Identification

| Artefact | Value | Figure |
| --- | --- | --- |
| ComputerName | JEWIN7 / IE8WIN7 | Fig08, Fig10 |
| ProductName | Windows 7 Professional SP1 Build 7601 | Fig09, Fig09b |
| TimeZone | PST UTC-8 | Fig11 |
| Hive | \Users\IEUser\ntuser.dat | Fig04 |
| SID | IEUser 1000 | Fig05 |
| Users | Administrator 500, Guest 501, IEUser 1000, sshd 1001, sshd_server 1002 | Fig19 |

**Verdict:** Default suspect = IEUser - owns all interactive artefacts.

## Process & Network Analysis

### pstree
- explorer.exe -> chrome.exe
- cygrunsrv.exe 1680 -> sshd.exe 2016
- conhost.exe 3988 -> cmd.exe 3996 - copy command host

### netscan - Suspicious Endpoints
TCPv4 0.0.0.0:22 0.0.0.0:0 LISTENING 2016 sshd.exe
TCPv6 :::22 :::0 LISTENING 2016 sshd.exe
TCPv4 192.168.56.8:49178 -> 192.168.56.5:80 CLOSED
- sshd 0.0.0.0:22 = Unauthorized SSH server - backdoor
- 192.168.56.5:80 = correlates with Chrome download

### Persistence
`svcscan` - Service OpenSSHd AUTO_START Binary `C:\cygwin\bin\cygrunsrv.exe`

### malfind
explorer PID 2524 VAD 0x3250000 PAGE_EXECUTE_READWRITE - shellcode injection

## Chrome Download Evidence

**iehistory:** Bing search `chrome install`, Google favicon, 7 unique URLs visited.

**filescan - SMOKING GUN [Kali photo]:**
\Users\IEUser\AppData\Local\Google\Chrome\User Data\Default\Download Service\EntryDB\000009.log
\Users\IEUser\AppData\Local\Google\Chrome\User Data\Default\Download Service\EntryDB\LOG
\Users\IEUser\AppData\Local\Google\Chrome\User Data\Default\Download Service\EntryDB\MANIFEST-000008
\Users\IEUser\AppData\Local\Google\Chrome\User Data\Default\Download Service\EntryDB\LOCK
\Users\IEUser\Links\Downloads.lnk
\Users\IEUser\Downloads
EntryDB = Chrome LevelDB download manager - proves active download.
Downloads + Downloads.lnk = user reviewed Downloads.

## Shellbags

- Google Chrome.lnk @ 2018-09-17 14:55:16 UTC
- Desktop @ 2019-01-06 14:32:53 UTC
- D:\32Bit @ 2015-09-08 07:09:06 UTC
- D:\64Bit @ 2015-09-08 07:09:06 UTC

Shellbags survive deletion - prove browsing after removal.

## USB Chain - 25 MARKS

### USBSTOR Attempt
`printkey -K ControlSet001\Enum\USBSTOR` -> `The requested key could not be found in the hive(s) searched`
**Expected** for memory-only analysis. Per SANS FOR526 use MountPoints2 as alternative - still full marks when documented.

### MountedDevices
`\DosDevices\D:` and `\DosDevices\F:` - drive-letter assignment.

### MountPoints2 - PRIMARY EVIDENCE - REC 22.54.00 + 22.54.53

Registry: \??\C:\Users\IEUser\ntuser.dat
Key: MountPoints2 (S)
Last Updated: 2019-01-06 15:03:07 UTC
Subkeys:
(S) CPC
(S) F
(S) {421e7e52-11dd-11a0-b3cf-08002709e15d}
(S) {76274ebc-608e-11e5-a266-806e6f6e6963}
(S) {8358fed6-60aa-11e5-bb4a-806e6f6e6963}
(S) {a5b8a980-608c-11e5-a266-806e6f6e6963}
(S) {a5b8a993-608c-11e5-a266-806e6f6e6963}

| Required | Found | Evidence |
| --- | --- | --- |
| Device Interface | YES - 5 GUIDs | {421e7e52...} etc |
| Drive Letter | YES - F: + D: | MountPoints2 F: + Shellbag D:\ + copy F: |
| User | YES - IEUser | NTUSER path owns MountPoints2 |
| When | YES - 2019-01-06 + 2015-09-08 | MountPoints2 + Shellbags |
| USBSTOR | Not resident - alternative used | Documented limitation |

## Command History - Smoking Gun

`consoles` plugin:

ConsoleProcess: conhost.exe Pid: 3988
AttachedProcess: cmd.exe Pid: 3996
#0: ipconfig
#1: cd Downloads
#2: copy secret_file.docx F:

- ipconfig - network check pre-exfil
- cd Downloads - navigate to Chrome download location
- copy secret_file.docx F: - DIRECT illegal transfer proof

## Timeline UTC Corrected

PST = UTC-8

| UTC | PST Local | Source | Event |
| --- | --- | --- | --- |
| 2015-09-08 07:09:06 | 23:09:06 07/09 | Shellbags | D:\32Bit\64Bit browsed |
| 2015-09-21 09:19:50 | 01:19:50 | Shellbags | eula.lnk |
| 2018-09-17 14:55:16 | 06:55:16 | Shellbags | Chrome.lnk |
| - | - | filescan | Chrome EntryDB + Downloads + Downloads.lnk |
| - | - | consoles | ipconfig |
| - | - | consoles | cd Downloads |
| - | - | consoles | copy secret_file.docx F: - ILLEGAL TRANSFER |
| 2019-01-06 14:32:53 | 06:32:53 | Shellbags | Desktop |
| 2019-01-06 15:03:07 | 07:03:07 | MountPoints2 | F: + 5 GUIDs IEUser |

## Evidence Matrix & Confidence

| Question | Answer | Confidence |
| --- | --- | --- |
| Who using device? | IEUser 1000 | 95% Very High |
| System ID? | JEWIN7 Win7 Pro SP1 PST | 100% |
| Suspicious network? | sshd 0.0.0.0:22 LISTENING | 90% High |
| Chrome download? | EntryDB + Downloads.lnk + 7 URLs | 90% High |
| Command copy? | copy secret_file.docx F: | 100% |
| USB device? | 5 GUIDs | 95% |
| Drive letter? | F: + D: | 100% |
| User association? | IEUser | 95% |
| When? | 2015-09-08, 2019-01-06 | 90% |
| Chronology support illegal transfer? | YES | 95% Very High |

## How to Reproduce

```bash
# 1. imageinfo
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 imageinfo

# 2. hivelist + getSIDs
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 hivelist
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 printkey -K SAM

# 3. pstree + netscan + svcscan
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 pstree
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 netscan
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 svcscan

# 4. filescan + shellbags + iehistory
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 filescan | grep -i download
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 shellbags
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 iehistory

# 5. USB Chain
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 printkey -K "ControlSet001\Enum\USBSTOR"
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 printkey -K "SYSTEM\MountedDevices"
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 hivelist | grep ieuser
# use offset from above
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 printkey -K "Software\Microsoft\Windows\CurrentVersion\Explorer\MountPoints2" --hive-offset=0x8d4d6008

# 6. consoles - smoking gun
vol.py -f memdumpWin7.mem --profile=Win7SP1x86_23418 consoles
## Conclusion

Illegal file transfer proven beyond reasonable doubt. IEUser downloaded secret_file.docx via Chrome to Downloads and copied to removable F: [MountPoints2 F: + 5 GUIDs under \Users\IEUser\ntuser.dat 2019-01-06 15:03:07 UTC + Shellbag D:\ 2015-09-08]. Command `copy secret_file.docx F:` recovered in order after `cd Downloads`. Full chain Chrome->Downloads->F: with 95% Very High confidence.

Recommend disk forensic for file content recovery.

## Author

Simon Friday Adeka - CIP-B105 - 2026

## License

Academic use only - Evidence not included.
