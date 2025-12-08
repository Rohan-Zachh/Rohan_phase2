# Advent of Cyber 2025

# Day 6 : Malware Analysis - Egg-xecutable
Discover some common tooling for malware analysis within a sandbox environment.

## Concepts Learnt:

- There are two main branches of malware analysis: static and dynamic. Static analysis focuses on inspecting a file without executing it, whereas dynamic analysis involves execution.
- Sandboxes: In cyber security, sandboxes are used to execute potentially dangerous code. Think of this as disposable digital play-pens. These sandboxes are safe, isolated environments where potentially malicious applications can perform their actions without risking sensitive data or impacting other systems.
- The use of sandboxes is part of the golden rule in malware analysis: never run dangerous applications on devices you care about.
- In static analysis, we look into the checksum, strings - mainly
- In dynamic analysis, we use regshot for our analysis.
- Regshot is a widely used utility, especially when analysing malware on Windows. It works by creating two "snapshots" of the registry—one before the malware is run and another afterwards. The results are then compared to identify any changes.
- We also use ProcMon to do dynamic analysis.
- we will explore using ProcMon (Process Monitor) from the Sysinternals suite to investigate today's sample. Proccess Monitor is used to monitor and investigate how processes are interacting with the Windows operating system. It is a powerful tool that allows us to see exactly what a process is doing. For example, reading and writing registry keys, searching for files, or creating network connections.

## Solution:
- For the static analysis challenges, we use pestudio in the VM and for the dynamic analysis challenges, we use regshot and procmon to do the required tasks.

1. Static analysis: What is the SHA256Sum of the HopHelper.exe?
- F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33
2. Static analysis: Within the strings of HopHelper.exe, a flag with the format THM{XXXXX} exists. What is that flag value?
- THM{STRINGS_FOUND}
3. Dynamic analysis: What registry value has the HopHelper.exe modified for persistence?
- HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper
4. Dynamic analysis: Filter the output of ProcMon for "TCP" operations. What network protocol is HopHelper.exe using to communicate?
- HTTP
5. Bonus: Can you find the web panel that HopHelper.exe is communicating with?
- yea, found it using the url given in the TCP channel.


# Day 7: Network Discovery - Scan-ta Clause
Discover how to scan network ports and uncover what is hidden behind them.

## Concepts Learnt: 
- The Simplest Port Scan: There are many tools you can use to scan for open ports, from preinstalled Netcat on Linux and PowerShell on Windows, to specialized, powerful tools like Nmap and Naabu.
- nmap : The command scanned the top 1000 most commonly used ports and reported if any services were running there.
- Scanning Whole Range: For that, we need to add the -p- argument to scan all ports, and --script=banner to see what's likely behind the port.
- FTP: File Transfer Protocol (FTP) is a protocol designed to help the efficient transfer of files between different and even non-compatible systems. It supports two modes for file transfer: binary and ASCII (text).
- ftp command- To implement the analysis on FTP protocols.