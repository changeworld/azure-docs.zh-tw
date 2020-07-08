---
title: 針對 OS 啟動進行疑難排解 - Windows Update 安裝容量
description: 解決 Windows Update (KB) 發生錯誤，且在 Azure VM 中變得沒有回應相關問題的步驟。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: bb7b641a7169c6577320f07a964d278ac1727b1c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663306"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>針對 OS 啟動進行疑難排解 - Windows Update 安裝容量

本文提供的步驟可解決 Azure 虛擬機器 (VM) 中的問題，其中 Windows Update (KB) 會發生錯誤且變得沒有回應。

## <a name="symptom"></a>徵狀

當您使用開機診斷來檢視 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows Update (KB) 進行中，但是失敗且具有以下錯誤碼：**C01A001D**。 下圖顯示 Windows Update (KB) 卡在下列訊息，「套用更新作業 ##### 之 ##### (######) 時發生錯誤 C01A001D」：

![Windows Update (KB) 卡在下列訊息，「套用更新作業 Y 之 X (Z) 時發生錯誤 C01A001D」。](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>原因

在此情況下，作業系統 (OS) 無法完成 Windows Update (KB) 安裝，因為無法在檔案系統上建立核心檔案。 根據此錯誤碼，作業系統無法將任何檔案寫入磁碟。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀：

1. 建立和存取修復 VM。
1. 磁碟上的可用空間。
1. 啟用序列主控台和記憶體傾印集合。
1. 重建 VM。

> [!NOTE]
> 發生此錯誤時，客體作業系統將無法運作。 請在離線模式中針對此問題進行疑難排解，以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用 [VM 修復命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)的步驟 1-3 準備修復 VM。
1. 使用遠端桌面連線，連線至修復 VM。

### <a name="free-up-space-on-the-disk"></a>釋放磁碟上的空間

若要解決此問題：

- 如果磁碟的大小上限不是 1 TB，請將其大小調整為 1 TB。
- 執行磁碟清理。
- 對磁碟機進行磁碟重組。

1. 檢查磁碟是否已滿。 如果磁碟大小低於 1 TB，請[使用 PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) 將其擴充至 1 TB 的上限。
1. 如果磁碟已經是 1 TB，您需要執行磁碟清理。
   1. [從中斷的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk) 卸離資料磁碟。
   1. 將資料磁碟連結[至運作中的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)。
   1. 使用[磁碟清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)釋放空間。
1. 調整大小和清除完成之後，請使用下列命令對磁碟機進行磁碟重組：

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
磁碟重組可能需要數小時的時間，視片段的層級而定。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>啟用序列主控台和記憶體傾印集合

**建議**：重建 VM 之前，請先藉由執行下列指令碼來啟用序列主控台和記憶體傾印集合：

1. 以系統管理員身分開啟提升權限的命令提示字元工作階段。
1. 執行下列命令：

   **啟用序列主控台**：
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. 確認作業系統磁碟上的可用空間大於 VM 上的記憶體大小 (RAM)。

   如果作業系統磁碟上沒有足夠的空間，請變更記憶體傾印檔案的建立位置，並將其指向任何已與具有足夠可用空間 VM 連結的資料磁碟位置。 若要變更位置，請使用下列命令，將 **%SystemRoot%** 取代為資料磁碟的磁碟機代號 (例如 **F:** )。

   啟用作業系統傾印的建議設定：

    **載入中斷的作業系統磁碟：**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **在 ControlSet001 上啟用**：

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **在 ControlSet002 上啟用**：

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **卸載中斷的作業系統磁碟**：

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 重建 VM。
