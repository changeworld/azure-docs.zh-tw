---
title: 針對開機錯誤進行疑難排解 - 發生磁碟讀取錯誤
description: 此文章提供的步驟可解決在 Azure VM 中無法讀取磁碟的問題。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: dea09b1ac29db99e1c52a31a605007fa4129e8ea
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84302600"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>針對開機錯誤進行疑難排解 - 發生磁碟讀取錯誤

此文章提供的步驟可解決在 Azure 虛擬機器 (VM) 中無法讀取磁碟的問題。

## <a name="symptoms"></a>徵狀

當您使用[開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) \(部分機器翻譯\) 來檢視 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示含有下列訊息的提示：「發生磁碟讀取錯誤。 按 Ctrl+Alt+Del 以重新啟動」。

   ![錯誤訊息：發生磁碟讀取錯誤。 按下 Ctrl+Alt+Del 重新啟動。](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>原因

此錯誤訊息表示磁碟結構已損毀且無法讀取。 如果您使用第 1 代 VM，也有可能是包含開機設定資料的磁碟分割未設定為 [使用中]。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 建立及存取修復 VM。
1. 選取解決方案：
   - [將磁碟分割狀態設定為使用中](#set-partition-status-to-active)
   - [修正磁碟分割](#fix-the-disk-partition)
1. 啟用序列主控台與記憶體傾印收集。
1. 重建 VM。

> [!NOTE]
> 發生此開機錯誤時，客體作業系統 (OS) 將無法運作。 您將在離線模式下進行疑難排解，以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 使用 [VM 修復命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。
1. 使用遠端桌面連線，連線至修復 VM。

### <a name="set-partition-status-to-active"></a>將磁碟分割狀態設定為使用中

第 1 代 VM 應先確認保存 BCD 存放區的 OS 磁碟分割已標示為 [使用中]。 如果您有第 2 代 VM，請直接跳到[修正磁碟分割](#fix-the-disk-partition)，因為狀態旗標在較新的世代中已過時。

1. 開啟提升權限的命令提示字元 (cmd.exe)。
1. 輸入 **diskpart** 以啟動 **DISKPART** 工具。
1. 輸入 **list disk** 以列出系統上的磁碟，並識別連結的 OS 虛擬硬碟 (VHD)。
1. 找到連結的 OS VHD 之後，請輸入 **sel disk #** 以選取磁碟。 如需範例，請參閱下圖，其中磁碟 1 是連結的 OS VHD。

   ![含有 **list disk** 命令輸出的 diskpart 視窗，其中以表格顯示磁碟 0 與磁碟 1。 此視窗也會顯示 **sel disk 1** 命令的輸出，其中磁碟 1 就是選取的磁碟](./media/disk-read-error-occurred/2.png)

1. 選取磁碟之後，請輸入 **list partition** 以列出所選擇磁碟的磁碟分割。
1. 識別出開機磁碟分割之後，請輸入 **sel partition #** 以選取該磁碟分割。 開機磁碟分割的大小通常約為 350 MB。  如需範例，請參閱下圖，其中磁碟分割 1 是開機磁碟分割。

   ![含有 **list partition** 命令輸出的 diskpart 視窗，其中以表格顯示磁碟分割 1 與磁碟分割 2。 此視窗也會顯示 **sel partition 1** 命令的輸出，其中磁碟分割 1 是選取的磁碟。](./media/disk-read-error-occurred/3.png)

1. 輸入 **detail partition** 以檢查磁碟分割的狀態。 請參閱下列磁碟分割範例的螢幕擷取畫面，磁碟分割已設定為 [使用中:否] 或 [使用中:是]。

   **使用中:否**

   ![含有 **detail partition** 命令輸出的 diskpart 視窗，其中磁碟分割 1 已設定為 [使用中:否]。](./media/disk-read-error-occurred/4.png)

   **使用中:是**

   ![含有 **detail partition** 命令輸出的 diskpart 視窗，其中磁碟分割 1 已設定為 [使用中:是]。](./media/disk-read-error-occurred/5.png)

1. 如果資料分割未設定為 [使用中]，請輸入 **active** 以變更 [使用中] 旗標。
1. 輸入 **detail partition** 以檢查是否正確完成狀態變更，並確認輸出包含 [使用中:是]。 
1. 輸入 **exit** 以關閉 DISKPART 工具，並儲存您的設定變更。

### <a name="fix-the-disk-partition"></a>修正磁碟分割

1. 開啟提升權限的命令提示字元 (cmd.exe)。
1. 使用下列命令，在磁碟上執行 **CHKDSK** 並執行錯誤修正：

   `chkdsk <DRIVE LETTER>: /f`

   新增 **/f** 命令選項將會修正磁碟上的任何錯誤。 請務必將 **< 磁碟機代號 >** 取代為所連結 OS VHD 的代號。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>啟用序列主控台與記憶體傾印收集

**建議**：重建 VM 之前，請先透過執行下列指令碼來啟用序列主控台與記憶體傾印收集：

1. 以系統管理員身分開啟提升權限的命令提示字元工作階段。
1. 執行下列命令：

   **啟用序列主控台**：
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. 確認 OS 磁碟上的可用空間大於 VM 上的記憶體大小 (RAM)。

   如果 OS 磁碟上沒有足夠的空間，請變更記憶體傾印檔案的建立位置，並將該位置指向任何已與 VM 連結且具有足夠可用空間的資料磁碟位置。 若要變更位置，請使用下列命令，將 **%SystemRoot%** 取代為資料磁碟的磁碟機代號 (例如 **F:** )。

   啟用 OS 傾印的建議設定：

   **從中斷的 OS 磁碟載入登錄區：**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **在 ControlSet001 上啟用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **在 ControlSet002 上啟用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **卸載中斷的 OS 磁碟：**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)\(部分機器翻譯\)重建 VM。
