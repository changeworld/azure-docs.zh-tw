---
title: 應用 Windows 更新時,Azure VM 無回應,出現 C01A001D 錯誤
description: 本文提供了解決 Windows 更新在 Azure VM 中生成錯誤且無回應的問題的步驟。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633953"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>應用 Windows 更新時,VM 無回應,出現「C01A001D」錯誤

本文提供了解決 Windows 更新 (KB) 在 Azure VM 中生成錯誤且無回應的問題的步驟。

## <a name="symptoms"></a>徵狀

當使用[引導診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)來查看 VM 的螢幕截圖時,將顯示正在進行的 Windows 更新 (KB),但錯誤代碼為「C01A001D」失敗。

![沒有回應的 Windows 更新](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>原因

無法在檔案系統中建立核心檔。 操作系統無法將檔案寫入磁碟。

## <a name="resolution"></a>解決方案

### <a name="process-overview"></a>程序概觀

1. [創建並訪問修復 VM。](#create-and-access-a-repair-vm)
2. [釋放硬碟上的空間](#free-up-space-on-the-hard-disk)。
3. [建議:在重建 VM 之前,請啟用串列主控台和記憶體轉儲集合](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)。
4. [重建 VM](#rebuild-the-vm)。

> [!NOTE]
> 發生此錯誤時,來賓操作系統無法運行。 您必須在離線模式下進行故障排除才能解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 按照[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)準備修復 VM。
2. 使用遠端桌面連接連接到修復 VM。

### <a name="free-up-space-on-the-hard-disk"></a>釋放硬碟上的空間

如果磁碟尚未 1 Tb,則必須調整其大小。 磁碟為 1 TB 後,執行磁碟清理和驅動器碎片整理。

1. 檢查磁碟是否已滿。 如果磁碟低於 1 Tb,[請使用 PowerShell 將其擴展至最大 1 Tb。](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
2. 磁碟為 1 Tb 後,執行磁碟清理。
    - [將資料磁碟從損毀的 VM 中分離](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)。
    - [將數據磁碟附加到正常運行的 VM。](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)
    - 使用[磁碟清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)釋放空間。
3. 調整大小和清理大小後,對驅動器進行碎片整理:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    根據碎片級別,這可能需要數小時。

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>建議:在重建 VM 之前,請啟用串列主控台和記憶體轉儲集合

1. 打開提升的命令提示會話(以管理員身份運行)。
2. 執行下列命令：

    開啟串列主控台:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. 確保 OS 磁碟上的可用空間至少等於 VM 記憶體 (RAM) 大小。

    如果 OS 磁碟上沒有足夠的空間,請更改將創建記憶體轉儲檔的位置,並將其引用到連接到 VM 且具有足夠可用空間的數據磁碟。 要變更位置,請用`%SystemRoot%`以下指令中資料磁碟的驅動器號(例如"F:")取代:

    **開啟作業系統傾印建議設定:**

    載入損壞的 OS 磁碟:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    在 ControlSet001 上開啟:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    在 ControlSet002 上開啟:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    卸載損壞的 OS 磁碟:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>重建 VM

使用[VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新組裝 VM。
