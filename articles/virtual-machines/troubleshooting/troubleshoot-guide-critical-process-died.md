---
title: 視窗停止錯誤 -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373358"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>視窗停止錯誤 - #0x000000EF"關鍵進程已死"

本文提供瞭解決在 Azure VM 中引導過程中關鍵進程死亡的問題的步驟。

## <a name="symptom"></a>徵狀

當您使用[引導診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)來查看 VM 的螢幕截圖時，您將看到螢幕截圖顯示錯誤 *#0x000000EF*與消息 *"關鍵進程已死*"。

![「您的電腦發生問題，並需要重新啟動。 我們只會收集一些錯誤資訊，然後您便可以重新啟動。 （=% 完成）如果您想瞭解更多，您可以稍後線上搜索此錯誤：0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>原因

通常，這是因為在引導過程中的關鍵系統進程失敗。 您可以在["Bug 檢查 0xEF：CRITICAL_PROCESS_DIED"](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)上閱讀更多有關關鍵流程問題的文章。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>流程概述：

1. 創建和訪問修復 VM。
2. 修復任何作業系統損壞。
3. **建議**：在重建 VM 之前，請啟用串列主控台和記憶體傾印集合。
4. 重建 VM。

> [!NOTE]
> 遇到此啟動錯誤時，客體作業系統無法運行。 您將在離線模式下進行故障排除以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>創建和訪問修復 VM

1. 使用[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="fix-any-os-corruption"></a>修復任何作業系統損壞

1. 開啟提升權限的命令提示字元。
2. 運行以下系統檔檢查器 （SFC） 命令：

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * 其中<引導磁片磁碟機>是修復 VM 的開機磁碟區（通常為"C："）和< BROKEN 磁片磁碟機>將是損壞的 VM 中連接的磁片的磁碟機號。 將大於 /小於符號的符號以及符號中包含的文本（例如"此處<文本>"替換為相應的字母。

3. 接下來，使用[VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新組裝 VM，並查看 VM 是否啟動。
4. 如果 VM 仍未啟動，則繼續收集記憶體傾印檔。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔

如果運行 SFC 後問題仍然存在，則需要分析記憶體傾印檔以確定問題的原因。 要收集記憶體傾印檔，請按照以下步驟操作：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將作業系統磁片連接到新的修復 VM

1. 使用[VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)準備新的修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到轉儲檔並提交支援票證

3. 在修復 VM 上，轉到連接的 OS 磁片中的視窗資料夾。 如果分配給附加的 OS 磁片的驅動程式字母為*F，* 則需要轉到*F：_Windows*。
4. 找到*記憶體.dmp*檔，然後提交包含記憶體傾印檔[的支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

   > [!NOTE]
   > 如果找不到轉儲檔，請完成以下步驟以啟用記憶體傾印集合和串列主控台，然後返回到此部分，並重複上述任務中的步驟以收集記憶體傾印檔。

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
