# EaseFilter Data Protection SDK (Windows)

[![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%7C%2011%20%7C%20Server%202016--2025-0078D6?logo=windows)](https://www.easefilter.com/)
[![Driver](https://img.shields.io/badge/Driver-Microsoft%20WHQL%20Certified-2ea44f)](https://www.easefilter.com/)
[![Languages](https://img.shields.io/badge/Languages-C%23%20%7C%20C%2B%2B%20%7C%20Python%20%7C%20Java%20%7C%20Rust-blue)](https://www.easefilter.com/)

**EaseFilter Data Protection SDK** is a unified, kernel-mode Windows security framework that lets developers build enterprise-grade **File Access Control**, **Transparent AES-256 File Encryption**, **Process Filtering**, and **Registry Filtering** in days—without writing a single line of kernel-mode C code.

---

## Architecture

EaseFilter uses a split-architecture model that separates your high-level security logic in user mode from native, tamper-resistant enforcement in the Windows kernel.

![EaseFilter Data Protection SDK Architecture](https://www.easefilter.com/Images/data-protection-sdk.jpg)

* **Kernel-Mode Driver (`EaseFlt.sys`):** A Microsoft WHQL-certified multi-layer filter driver that registers natively with the Windows I/O Manager, Process/Thread Manager, and Configuration Manager.
* **User-Mode API (`FilterAPI.dll` / `FilterControl`):** Clean managed and native interfaces that let you define static kernel policies or handle real-time pre/post I/O callbacks in C#, C++, Python, Java, or Rust.

---

## Core Modules

| Module | Key Capabilities |
| :--- | :--- |
| **File Access Control SDK** | Intercept Pre-IO and Post-IO file operations in real time. Block unauthorized reads, writes, renames, deletions, or exfiltration based on Active Directory user, process name, certificate, or file path. Hide sensitive files and folders from untrusted apps. |
| **Transparent File Encryption (TFE)** | On-the-fly, sector-level **AES-256** encryption and decryption. Authorized processes read plaintext seamlessly in memory while physical storage holds only ciphertext. Supports zero-trust decryption and DRM policies. |
| **Process Filter Driver** | Intercept process and thread creation/termination system-wide. Block untrusted or unsigned binaries, stop vulnerable apps (e.g., browsers) from spawning shells, and protect your security agent from termination or injection. |
| **Registry Filter Driver** | Monitor and lock down Windows Registry keys at the kernel level. Block unauthorized writes to startup `Run` keys, Services, and security policies to prevent malware persistence and configuration tampering. |
| **File Monitor & Auditing** | Real-time and deferred file activity logging (create, read, write, rename, delete) for SIEM integration, compliance auditing, and anti-ransomware behavior detection. |

---

## Quick Start (C#)

The example below configures **File Access Control + Transparent AES-256 Encryption**, **Process Filtering**, and **Registry Filtering** in a single user-mode application:

```csharp
using System.Text;
using EaseFilter.FilterControl;

public class DataProtectionDemo
{
    public static void Main()
    {
        // Initialize the EaseFilter Data Protection SDK
        FilterControl filter = new FilterControl();

        // 1. File Access Control + Transparent AES-256 Encryption
        FileFilter fileRule = new FileFilter(@"c:\sensitive_data\*.*");
        fileRule.EnableEncryption = true;
        fileRule.EncryptionKey = Encoding.UTF8.GetBytes("Your256BitEncryptionKeyHere!");

        // Block delete, rename, and decryption for untrusted processes
        fileRule.AccessFlags = FilterAPI.ALLOW_OPEN_WITH_READ_ACCESS;
        fileRule.AddTrustedProcessRight(
            (uint)FilterAPI.ALLOW_MAX_RIGHT_ACCESS,
            "trusted_app.exe",
            "Your Company Corp",
            ""
        );
        filter.AddFilter(fileRule);

        // 2. Process Filter: Block untrusted executables from Downloads
        ProcessFilter procRule = new ProcessFilter(@"c:\users\*\downloads\*.exe");
        procRule.ControlFlag = (uint)FilterAPI.ProcessControlFlag.DENY_NEW_PROCESS_CREATION;
        filter.AddFilter(procRule);

        // 3. Registry Filter: Block unauthorized persistence in Run keys
        RegistryFilter regRule = new RegistryFilter();
        regRule.RegKeyNameFilterMask = @"*\SOFTWARE\Microsoft\Windows\CurrentVersion\Run*";
        regRule.ControlFlag = (uint)FilterAPI.RegControlFlag.REG_DENY_SET_VALUE_KEY;
        filter.AddFilter(regRule);

        // Start all kernel-mode protection filters
        filter.StartFilter();
    }
}
```

---

## Enterprise Use Cases

* **Anti-Ransomware & Malware Defense:** Combine file access control with process and registry filtering to block unauthorized file encryption, stop suspicious executables, and prevent registry persistence.
* **Zero-Trust DLP & Compliance:** Meet HIPAA, GDPR, and SOC 2 requirements with transparent AES-256 encryption and granular, user/process-scoped access policies.
* **Endpoint Agent Self-Protection & DRM:** Shield your application's files, processes, and registry keys from tampering while enforcing strict DRM on shared documents.

---

## System Requirements & Compatibility

* **Operating Systems:** Windows 7 / 10 / 11 (64-bit) and Windows Server 2016 – 2025 (64-bit)
* **Driver Signing:** Microsoft WHQL Certified
* **Supported Languages:** C# / .NET, C++, Python, Java, Go, Rust

---

## Download & Documentation

* **Free 30-Day Trial (Installer):** [Download EaseFilter SDK (.exe)](https://www.easefilter.com/download.htm)
* **Developer Guide:** [EaseFilter File Security SDK Developer's Guide](https://www.easefilter.com/kb/file-security-sdk-developer-guide.htm)
* **Module Docs:**
  * [File Control Filter Driver SDK](https://www.easefilter.com/kb/file-control-file-security-sdk.htm)
  * [Transparent File Encryption SDK](https://www.easefilter.com/kb/transparent-file-encryption-filter-driver-sdk.htm)
  * [Process Filter Driver SDK](https://www.easefilter.com/kb/process-filter-driver-sdk.htm)
  * [Registry Filter Driver SDK](https://www.easefilter.com/kb/registry-filter-drive-sdk.htm)

---
## EaseFilter File System Filter Driver SDK Reference
| Product Name | Description |
| --- | --- |
| [Cloud File System SDK](https://www.easefilter.com/cloud/cloud-file-system-sdk.htm) | EaseFilter Cloud File System SDK Introduction. |
| [CloudTier Storage Tiering SDK](https://www.easefilter.com/cloud/storage-tiering-sdk.htm) | EaseFilter Storage Tiering Filter Driver SDK Introduction. |
| [File Monitor SDK](https://www.easefilter.com/kb/file-monitor-filter-driver-sdk.htm) | EaseFilter File Monitor Filter Driver SDK Introduction. |
| [File Control SDK](https://www.easefilter.com/kb/file-control-file-security-sdk.htm) | EaseFilter File Control Filter Driver SDK Introduction. |
| [File Encryption SDK](https://www.easefilter.com/kb/transparent-file-encryption-filter-driver-sdk.htm) | EaseFilter Transparent File Encryption Filter Driver SDK Introduction. |
| [Registry Filter SDK](https://www.easefilter.com/kb/registry-filter-drive-sdk.htm) | EaseFilter Registry Filter Driver SDK Introduction. |
| [Process Filter SDK](https://www.easefilter.com/kb/process-filter-driver-sdk.htm) | EaseFilter Process Filter Driver SDK Introduction. |
| [EaseFilter SDK Programming](https://www.easefilter.com/kb/programming.htm) | EaseFilter Filter Driver SDK Programming. |

## EaseFilter SDK Sample Projects
| Sample Project | Description |
| --- | --- |
| [CloudTier Storage Tiering Demo](https://www.easefilter.com/cloud/cloudtier-storage-tiering-demo.htm) | A HSM File System Filter Driver Demo. |
| [CloudTier S3 Tiering Demo](https://www.easefilter.com/cloud/cloudtier-s3-intelligent-tiering-demo.htm) | CloudTier S3 Intelligent Tiering Demo. |
| [Cloud File DR S3 Demo](https://www.easefilter.com/cloud/cloud-file-dr-demo.htm) | Cloud File DR S3 Demo. |
| [Amazon S3 File Explorer Demo](https://www.easefilter.com/cloud/s3-browser-demo.htm) | Amazon S3 File Explorer Demo. |
| [Auto File DRM Encryption](https://www.easefilter.com/kb/auto-file-drm-encryption-tool.htm) | Auto file encryption with DRM data embedded. |
| [Transparent File Encrypt](https://www.easefilter.com/kb/AutoFileEncryption.htm) | Transparent on access file encryption. |
| [Secure File Sharing with DRM](https://www.easefilter.com/kb/DRM_Secure_File_Sharing.htm) | Secure encrypted file sharing with digital rights management. |
| [File Monitor Example](https://www.easefilter.com/kb/file-monitor-demo.htm) | Monitor file system I/O in real time, tracking file changes. |
| [File Protector Example](https://www.easefilter.com/kb/file-protector-demo.htm) | Prevent sensitive files from being accessed by unauthorized users or processes. |
| [FolderLocker Example](https://www.easefilter.com/kb/FolderLocker.htm) | Lock file automatically in a FolderLocker. |
| [Process Monitor](https://www.easefilter.com/kb/Process-Monitor.htm) | Monitor the process creation and termination, block unauthorized process running. |
| [Registry Monitor](https://www.easefilter.com/kb/RegMon.htm) | Monitor the Registry activities, block the modification of the Registry keys. |
| [Secure Sandbox Example](https://www.easefilter.com/kb/Secure-Sandbox.htm) |A secure sandbox example, block the processes accessing the files out of the box. |
| [FileSystemWatcher Example](https://www.easefilter.com/kb/FileSystemWatcher.htm) | File system watcher, logging the file I/O events. |
| [ZeroTrust Example](https://www.easefilter.com/kb/zero-trust-file-access-control-demo.htm) | Zero trust file access control with encryption feature. |

## Filter Driver Reference

* [Understand MiniFilter Driver](https://www.easefilter.com/kb/understand-minifilter.htm)
* [Understand File I/O](https://www.easefilter.com/kb/File_IO.htm)
* [Understand I/O Request Packets(IRPs)](https://www.easefilter.com/kb/understand-irps.htm)
* [Filter Driver Developer Guide](https://www.easefilter.com/kb/DeveloperGuide.htm)
* [MiniFilter Filter Driver Framework](https://www.easefilter.com/kb/minifilter-framework.htm)
* [Isolation Filter Driver](https://www.easefilter.com/kb/Isolation_Filter_Driver.htm)

## License

This repository contains two distinct licensing categories:

1. **Wrapper/Implementation Code (MIT License):** 
   The source code, scripts, and configuration files provided directly in this repository are licensed under the [MIT License](LICENSE). You are free to modify, distribute, and use this specific integration code.

2. **EaseFilter SDK (Commercial License):**
   This project requires the EaseFilter SDK (including its binaries, filter drivers, and core libraries) to function. The EaseFilter SDK is **proprietary commercial software** and is strictly excluded from the MIT License.   
   To use the EaseFilter SDK for commercial purposes or in a production environment, you must obtain a valid commercial license directly from [EaseFilter](https://www.easefilter.com/license.pdf).

## Support
If you have questions or need help, please contact support@easefilter.com 

[Home](https://www.easefilter.com/) | [Solution](https://www.easefilter.com/solutions.htm) | [Download](https://www.easefilter.com/download.htm) | [Demos](https://www.easefilter.com/online-fileio-test.aspx) | [Blog](https://blog.easefilter.com/) | [Programming](https://www.easefilter.com/kb/programming.htm)
