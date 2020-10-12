---
title: Windows 停止錯誤-
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
ms.openlocfilehash: c04f3b27c7214dcf821c7698796bfaea399b947d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509098"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows 停止錯誤-#0x000000EF "Critical Process 壞掉"

本文提供的步驟可解決重要程式在 Azure VM 中開機期間發生錯誤的問題。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示錯誤 *#0x000000EF* 訊息 *重要進程壞掉*。

![「您的電腦發生問題，並需要重新啟動。 我們只會收集一些錯誤資訊，然後您便可以重新啟動。  ( # #% 完成) 如果您想要深入瞭解，您可以稍後在線上搜尋此錯誤：0x000000EF」](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>原因

這通常是因為開機期間的重要系統進程失敗。 您可以在「[Bug 檢查0xEF： CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)」中深入瞭解重要的進程問題。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀：

1. 建立及存取修復 VM。
2. 修正任何作業系統損毀。
3. **建議**：重建 VM 之前，請先啟用序列主控台和記憶體傾印集合。
4. 重建 VM。

> [!NOTE]
> 當遇到此開機錯誤時，虛擬作業系統無法運作。 您將會在離線模式中進行疑難排解以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立和存取修復 VM

1. 使用 [VM 修復命令的步驟 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 準備修復 VM。
2. 使用遠端桌面連線連線至修復 VM。

### <a name="fix-any-os-corruption"></a>修正任何作業系統損毀

1. 開啟提升權限的命令提示字元。
2. 執行下列系統檔案檢查工具 (SFC) 命令：

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * 其中 < 的開機磁片磁碟機 > 是修復 VM 的開機磁碟區 (通常是 "C：" ) 和 < 磁片磁碟機中斷 > 將會是已中斷 VM 的連接磁片磁碟機號。 將大於/小於符號以及其中包含的文字（例如 "< 這裡的文字 >"）取代為適當的字母。

3. 接下來，請使用 [Vm 修復命令的步驟 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 來重組 vm，並查看它是否開機。
4. 如果 VM 仍無法開機，請繼續收集記憶體傾印檔案。

### <a name="collect-the-memory-dump-file"></a>收集記憶體傾印檔案

如果在執行 SFC 之後問題持續發生，將需要分析記憶體傾印檔案，以判斷問題的原因。 若要收集記憶體傾印檔案，請遵循下列步驟：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 使用 [VM Repair 命令的步驟 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 來準備新的修復 VM。
2. 使用遠端桌面連線連線至修復 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

3. 在修復 VM 上，移至連接的 OS 磁片中的 [windows] 資料夾。 如果指派給已連結 OS 磁片的磁碟機號是 *F*，您需要移至 *F:\Windows*。
4. 找出 *記憶體 dmp* 檔案，然後使用記憶體傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

   > [!NOTE]
   > 如果您找不到傾印檔案，請完成下列步驟來啟用記憶體傾印收集和序列主控台，然後返回此區段並重複上述工作中的步驟，以收集記憶體傾印檔案。

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
