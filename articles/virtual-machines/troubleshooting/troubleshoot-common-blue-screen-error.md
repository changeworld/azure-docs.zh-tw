---
title: 將 Azure 虛擬機器開機時的藍色畫面錯誤 | Microsoft Docs
description: 了解如何針對在開機時收到藍色畫面錯誤問題的疑難排解 |Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 9a95ddf882e5edba9daa8ff91c02d1df1f50bceb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632971"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>當 Azure VM 開機時 Windows 顯示藍色畫面錯誤
本文描述當您在 Microsoft Azure 中將 Windows 虛擬機器 (VM) 開機時可能碰到的藍色畫面錯誤。 它提供步驟，以協助您收集支援票證的資料。 


## <a name="symptom"></a>徵狀 

Windows 虛擬機器未啟動。 當您檢查[開機診斷](./boot-diagnostics.md)中的開機螢幕擷取畫面時，您在藍色畫面中看到下列其中一個錯誤訊息：

- 我們的電腦遇到問題，且必須重新啟動。 我們只會收集一些錯誤資訊，然後您便可以重新啟動。
- 您的電腦遇到問題，且必須重新啟動。

本節列出管理 VM 時可能遇到的常見錯誤訊息：

## <a name="cause"></a>原因

您會收到停止錯誤可能有多個原因。 最常見的原因包括：

- 驅動程式問題
- 系統檔案或記憶體損毀
- 應用程式存取記憶體中禁止的磁區

## <a name="collect-memory-dump-file"></a>收集記憶體傾印檔

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

若要解決此問題，您需要先收集損毀傾印檔案，並連絡支援人員同時提供傾印檔案。 若要收集傾印檔案，請遵循下列步驟：

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。
2. [將 OS 磁片連結至復原 VM](./troubleshoot-recovery-disks-portal-windows.md)。 
3. 以遠端桌面連線到復原 VM。

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案，並提交支援票證

1. 在復原 VM 上，移至已連結 OS 磁碟的 Windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。
2. 找出記憶體 dmp 檔案，然後使用傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。 

如果找不到傾印檔案，請移到下一個步驟來啟用傾印記錄檔和序列主控台。

### <a name="enable-dump-log-and-serial-console"></a>啟用傾印記錄檔和序列主控台

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。
2. 執行下列指令碼：

    在此指令碼中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將它取代為您 VM 中的適當值。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. 確定磁碟上有足夠的空間可配置和 RAM 一樣多的記憶體，這取決於您為此 VM 選取的大小。
    2. 如果沒有足夠的空間，或這是大型 VM (G、GS 或 E 系列)，則您可以變更此檔案將建立的位置，並將它參考至連結到 VM 的其他任何資料磁碟。 若要這樣做，您必須變更下列機碼：

    ```config-reg
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

    reg unload HKLM\BROKENSYSTEM
    ```

3. [卸離 OS 磁碟，然後將 OS 磁碟重新連結至受影響的 VM](./troubleshoot-recovery-disks-portal-windows.md)。
4. 啟動 VM 以重現問題，然後便會產生傾印檔案。
5. 將 OS 磁碟連結至復原 VM、收集傾印檔案，然後[提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)同時提供傾印檔案。
