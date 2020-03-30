---
title: 由於 Windows 引導管理器，Windows 虛擬機器無法啟動
description: 本文提供瞭解決 Windows 引導管理器阻止啟動 Azure 虛擬機器的問題的步驟。
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
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373345"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>由於 Windows 引導管理器，Windows VM 無法啟動

本文提供瞭解決 Windows 引導管理器阻止啟動 Azure 虛擬機器 （VM） 的問題的步驟。

## <a name="symptom"></a>徵狀

VM 卡在使用者提示後處於等待，除非手動指示，否則不會啟動。

當您使用[引導診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)來查看 VM 的螢幕截圖時，您將看到螢幕截圖顯示 Windows 啟動管理器，並顯示消息 *"選擇要啟動的作業系統"，或者按 TAB 選擇工具：*。

圖 1
 
![Windows 啟動管理器指出"選擇要啟動的作業系統，或按 TAB 選擇工具：](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>原因

該錯誤是由於 Windows 啟動管理器中的 BCD 標誌*顯示引導功能表*造成的。 啟用標誌後，Windows 引導管理器會提示使用者在引導過程中選擇要運行的載入程式，從而導致啟動延遲。 在 Azure 中，此功能可以添加到啟動 VM 所需的時間。

## <a name="solution"></a>解決方法

流程概述：

1. 使用串列主控台配置更快的啟動時間。
2. 創建和訪問修復 VM。
3. 配置修復 VM 上的啟動時間更快。
4. **建議**：在重建 VM 之前，請啟用串列主控台和記憶體傾印集合。
5. 重建 VM。

### <a name="configure-for-faster-boot-time-using-serial-console"></a>使用串列主控台配置更快的啟動時間

如果您有權訪問串列主控台，可通過兩種方式實現更快的啟動時間。 減少*顯示引導功能表*等待時間，或完全刪除標誌。

1. 按照指示訪問[Windows 的 Azure 串列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)以訪問基於文本的主控台。

   > [!NOTE]
   > 如果無法訪問串列主控台，請跳到"[創建和訪問修復 VM"。](#create-and-access-a-repair-vm)

2. **選項 A**： 減少等待時間

   a. 預設情況下，等待時間設置為 30 秒，但可以更改為更快速的時間（例如 5 秒）。

   b. 在串列主控台中使用以下命令來調整超時值：

      `bcdedit /set {bootmgr} timeout 5`

3. **選項 B**： 刪除 BCD 標誌

   a. 要完全防止顯示引導功能表提示，請輸入以下命令：

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 如果無法使用串列主控台在上述步驟中配置更快的啟動時間，則可以繼續執行以下步驟。 現在，您將在離線模式下進行故障排除以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>創建和訪問修復 VM

1. 使用[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>配置修復 VM 上的更快的啟動時間

1. 開啟提升權限的命令提示字元。
2. 輸入以下內容以啟用 DisplayBootMenu：

   對於**第 1 代 VM**使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   對於**第 2 代 VM**使用此命令 ：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   替換任何大於或小於符號以及符號內的文本，例如"<文本>"。

3. 將超時值更改為 5 秒：

   對於**第 1 代 VM**使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   對於**第 2 代 VM**使用此命令 ：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   替換任何大於或小於符號以及符號內的文本，例如"<文本>"。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建議：在重建 VM 之前，請啟用串列主控台和記憶體傾印集合

要啟用記憶體傾印集合和串列主控台，請運行以下腳本：

1. 打開提升的命令提示會話（以管理員身份運行）。
2. 執行下列命令：

   啟用串列主控台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   替換任何大於或小於符號以及符號內的文本，例如"<文本>"。

3. 驗證 OS 磁片上的可用空間是否與 VM 上的記憶體大小 （RAM） 相同。

   如果 OS 磁片上沒有足夠的空間，則應更改將創建記憶體傾印檔的位置，並將其引用到連接到具有足夠可用空間的 VM 的任何資料磁片。 要更改位置，請將"%SystemRoot%"替換為以下命令中資料磁片的磁碟機號（例如"F："）。

#### <a name="suggested-configuration-to-enable-os-dump"></a>啟用 OS 轉儲的建議配置

**載入損壞的 OS 磁片**：

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**在 ControlSet001 上啟用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**在 ControlSet002 上啟用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**卸載損壞的 OS 磁片：**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>重建原始 VM

使用[VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新組裝 VM。