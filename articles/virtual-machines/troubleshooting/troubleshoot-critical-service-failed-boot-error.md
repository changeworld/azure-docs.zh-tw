---
title: 將 Azure VM 開機時發生 CRITICAL SERVICE FAILED | Microsoft Docs
description: 了解如何為開機時發生的 "0x0000005A-CRITICAL SERVICE FAILED" 錯誤進行疑難排解 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 8c3e76f1a7edffefc8773dfa548773ec0932fae6
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86129856"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>將 Azure VM 重新開機時，Windows 會在藍色畫面上顯示 "CRITICAL SERVICE FAILED"
本文說明當您在 Microsoft Azure 中將 Windows 虛擬機器 (VM) 開機時可能發生的 "CRITICAL SERVICE FAILED" 錯誤。 文中會提供疑難排解步驟，以協助解決問題。 


## <a name="symptom"></a>徵狀 

Windows 虛擬機器未啟動。 當您檢查[開機診斷](./boot-diagnostics.md)中的開機螢幕擷取畫面時，您會在藍色畫面中看到下列其中一項錯誤訊息：

- 「您的電腦發生問題，並需要重新啟動。 您可以重新啟動。 如需此問題及可能修正的詳細資訊，請前往 https://windows.com/stopcode。 若您連絡支援人員，請向其提供此資訊: 停止代碼: CRITICAL SERVICE FAILED」 
- 「您的電腦發生問題，並需要重新啟動。 我們只會收集一些錯誤資訊，然後會為您重新啟動。 若要深入了解，您可稍後線上搜尋此錯誤: CRITICAL_SERVICE_FAILED」

## <a name="cause"></a>原因

停止錯誤可能由各種原因所引發。 最常見的原因包括：
- 驅動程式問題
- 系統檔案或記憶體損毀
- 應用程式存取記憶體的禁止磁區

## <a name="solution"></a>解決方案 

若要解決此問題，請[連絡支援人員並提交傾印檔案](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)，這有助我們更快診斷問題，或是嘗試下列自助解決方案。

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。
2. [將 OS 磁片連結至復原 VM](./troubleshoot-recovery-disks-portal-windows.md)。 
3. 對復原 VM 建立遠端桌面連線。

### <a name="enable-dump-logs-and-serial-console"></a>啟用傾印記錄和序列主控台

傾印記錄檔及[序列主控台](./serial-console-windows.md)有助我們進行進一步的疑難排解。

若要啟用傾印記錄與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。
2. 執行下列指令碼：

    在此指令碼中，我們假設指派給所連結 OS 磁碟的磁碟機代號是 F。您應將其取代為適合您 VM 的值。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>取代未簽署的驅動程式

1. 在復原 VM 上，從提升權限的命令提示字元執行下列命令。 此命令會設定受影響的 OS 磁碟在下次開機時啟動至安全模式：

    ```console
    bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal
    ```

    例如，若您連結的 OS 磁碟為磁碟機 F，請執行下列命令：

    ```console
    bcdedit /store F: boot\bcd /set {default} safeboot minimal
    ```

2. 卸[離 os 磁片，然後將 os 磁片重新連結至受影響的 VM](troubleshoot-recovery-disks-portal-windows.md)。 VM 會開機進入安全模式。 若錯誤持續發生，請前往選用步驟。
3. 開啟 [執行]**** 方塊，並執行 [驗證器]**** 以啟動磁碟機驗證器管理工具。
4. 選取 [自動選取未簽署的驅動程式]****，然後按一下 [下一步]****。
5. 您會取得未簽署的驅動程式檔案清單。 記下檔案名稱。
6. 從運作中 VM 複製這些檔案的相同版本，然後取代這些未簽署的檔案。 

7. 移除安全開機設定：

    ```console
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
    ```

8.  重新啟動 VM。 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>選用：分析傾印損毀模式中的傾印記錄

若要自行分析傾印記錄，請遵循下列步驟：

1. 將 OS 磁碟連結至還原 VM。
2. 在您連接的 OS 磁片上，流覽至**\windows\system32\config**。複製所有檔案作為備份，以防需要復原。
3. 啟動 [登錄編輯程式]**** (regedit.exe)。
4. 選取 [HKEY_LOCAL_MACHINE]**** 機碼。 在功能表上 **，選取 [** 檔案] [  >  **載入 Hive**]。
5. 流覽至您連接的 OS 磁片上的**\windows\system32\config\SYSTEM**資料夾。 針對 Hive 的名稱，輸入 **BROKENSYSTEM**。 新的 Hive 會顯示在 [HKEY_LOCAL_MACHINE]**** 機碼下方。
6. 瀏覽至 **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** 並進行下列變更：

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  選取 [BROKENSYSTEM]****。 從功能表中 **，選取**  >  **[** 檔案] [卸載 Hive]。
8.  修改 BCD 設定以開機進入偵錯模式。 從提升權限的命令提示字元執行下列命令：

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. 卸[離 os 磁片，然後將 os 磁片重新連結至受影響的 VM](troubleshoot-recovery-disks-portal-windows.md)。
10. 將 VM 開機，以查看其是否顯示傾印分析。 尋找無法載入的檔案。 您需要以運作中 VM 的檔案取代此檔案。 

    下列是傾印分析範例。 您可看到 **FAILURE** 位於 filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys" 上。

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. 當 VM 正常運作及開機後，請移除傾印損毀設定：

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
