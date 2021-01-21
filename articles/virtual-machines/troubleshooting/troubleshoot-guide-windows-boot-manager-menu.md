---
title: Windows 虛擬機器無法開機，因為 windows 開機管理程式
description: 本文提供的步驟可解決 Windows 開機管理程式無法啟動 Azure 虛擬機器的問題。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5f83f4871d5cde23194ff51a90a22031b526cf91
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632558"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM 無法開機，因為 Windows 開機管理程式

本文提供的步驟可解決 Windows 開機管理程式無法 (VM) 啟動的問題。

## <a name="symptom"></a>徵狀

除非手動指示，否則 VM 會停滯在等候使用者提示字元，而且不會開機。

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 開機管理程式，並顯示訊息 *選擇要啟動的作業系統，或按 tab 鍵選取工具：*。

圖 1
 
![Windows 開機管理程式表示「選擇要啟動的作業系統，或按 TAB 鍵選取工具：」](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>原因

此錯誤是因為 Windows 開機管理程式中的 BCD 旗標 *displaybootmenu* 所造成。 啟用旗標之後，Windows 開機管理員會在開機過程中提示使用者，以選取要執行的載入器，而導致開機延遲。 在 Azure 中，這項功能可以新增到啟動 VM 所需的時間。

## <a name="solution"></a>解決方案

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

程序概觀：

1. 使用序列主控台設定更快速的開機時間。
2. 建立及存取修復 VM。
3. 設定修復 VM 的開機時間更快。
4. **建議**：重建 VM 之前，請先啟用序列主控台和記憶體傾印集合。
5. 重建 VM。

### <a name="configure-for-faster-boot-time-using-serial-console"></a>使用序列主控台設定更快速的開機時間

如果您可以存取序列主控台，有兩種方式可以達到更快的開機時間。 請減少 *displaybootmenu* 的等候時間，或完全移除旗標。

1. 遵循指示來存取 [適用于 Windows 的 Azure 序列主控台](./serial-console-windows.md) ，以取得以文字為基礎之主控台的存取權。

   > [!NOTE]
   > 如果您無法存取序列主控台，請直接跳到 [建立和存取修復 VM](#create-and-access-a-repair-vm)。

2. **選項 A**：減少等候時間

   a. 根據預設，等候時間設定為30秒，但可以變更為更快速的時間 (例如5秒) 。

   b. 在序列主控台中使用下列命令來調整超時值：

      `bcdedit /set {bootmgr} timeout 5`

3. **選項 B**：移除 BCD 旗標

   a. 若要完全防止顯示開機功能表提示，請輸入下列命令：

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 如果您在上述步驟中無法使用序列主控台設定更快速的開機時間，您可以改為繼續進行下列步驟。 您現在會在離線模式中進行疑難排解，以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用 [VM 修復命令的步驟 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 準備修復 VM。
2. 使用遠端桌面連線連接至修復 VM。

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>設定修復 VM 的開機時間更快

1. 開啟提升權限的命令提示字元。
2. 輸入下列內容以啟用 DisplayBootMenu：

   針對 **第1代 vm** 使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   針對 **第2代 vm** 使用此命令：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   取代任何大於或小於符號以及其中的文字，例如 "< 這裡的文字 >"。

3. 將 timeout 值變更為5秒：

   針對 **第1代 vm** 使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   針對 **第2代 vm** 使用此命令：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   取代任何大於或小於符號以及其中的文字，例如 "< 這裡的文字 >"。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建議：在重建 VM 之前，請先啟用序列主控台和記憶體傾印收集

若要啟用記憶體傾印收集和序列主控台，請執行下列腳本：

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。
2. 執行下列命令：

   啟用序列主控台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   取代任何大於或小於符號以及其中的文字，例如 "< 這裡的文字 >"。

3. 確認 OS 磁片上的可用空間與 VM 上 (RAM) 的記憶體大小一樣多。

   如果 OS 磁片上沒有足夠的空間，您應該變更將建立記憶體傾印檔案的位置，並將其參考到連接至 VM 且具有足夠可用空間的任何資料磁片。 若要變更位置，請將 "% SystemRoot%" 取代為磁碟機號 (例如，以下命令中的資料磁片 "F：" ) 。

#### <a name="suggested-configuration-to-enable-os-dump"></a>啟用 OS 傾印的建議設定

**載入中斷的作業系統磁碟**：

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**在 ControlSet001 上啟用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**在 ControlSet002 上啟用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**卸載中斷的 OS 磁碟：**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>重建原始 VM

使用 [VM 修復命令的步驟 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新組裝 VM。
