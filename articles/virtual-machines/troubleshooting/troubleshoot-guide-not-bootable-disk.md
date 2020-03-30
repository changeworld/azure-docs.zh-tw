---
title: 啟動錯誤 = "這不是可引導磁片"
description: 本文提供瞭解決磁片在 Azure 虛擬機器中無法啟動的問題的步驟
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300975"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>啟動錯誤 = 這不是可引導磁片

本文提供瞭解決磁片在 Azure 虛擬機器 （VM） 中無法啟動的問題的步驟。

## <a name="symptoms"></a>徵狀

當您使用[Boot 診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)來查看 VM 的螢幕截圖時，您將看到螢幕截圖顯示提示，提示顯示消息"這不是可引導磁片"。 請插入可啟動軟碟，然後按任意鍵重試..."。

   圖 1

   ![圖 1 顯示了消息 #"這不是可引導磁片。 請插入可啟動軟碟，然後按任意鍵重試..."。](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>原因

此錯誤訊息表示作業系統啟動進程找不到活動系統磁碟分割。 此錯誤還可能意味著啟動配置資料 （BCD） 存儲中缺少引用，從而阻止它定位 Windows 分區。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 創建和訪問修復 VM。
2. 將分區狀態設置為"活動"。
3. 修復磁碟分割。
4. **建議**：在重建 VM 之前，請啟用串列主控台和記憶體傾印集合。
5. 重建原始 VM。

   > [!NOTE]
   > 遇到此啟動錯誤時，客體作業系統無法運行。 您將在離線模式下進行故障排除以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>創建和訪問修復 VM

1. 使用[VM 修復命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)的步驟 1-3 準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="set-partition-status-to-active"></a>將分區狀態設置為活動

第 1 代 VM 應首先驗證保存 BCD 存儲的作業系統分區是否標記為*活動*。 如果您有第 2 代 VM，請跳到修復[磁碟分割](#fix-the-disk-partition)，因為*狀態*標誌在後一代中被棄用。

1. 打開提升的命令提示*符 （cmd.exe）。*
2. 輸入*磁片部分*以啟動 DISKPART 工具。
3. 輸入*清單磁片*以列出系統上的磁片並標識附加的 OS VHD。
4. 找到連接的 OS VHD 後，輸入*sel 磁片 +* 以選擇磁片。  參見圖 2，其中磁片 1 是附加的 OS VHD。

   圖 2

   ![圖 2 顯示了表中顯示清單磁片命令、磁片 0 和磁片 1 的輸出的 [DISKPART] 視窗。  還顯示 sel 磁片 1 命令的輸出，磁片 1 是所選磁片](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. 選擇磁片後，輸入*清單分區*以列出所選磁片的分區。
6. 識別引導分區後，輸入*sel 分區 +* 以選擇分區。  通常引導分區的大小約為 350 MB。  參見圖 3，其中分區 1 是引導分區。

   圖 3

   ![圖 3 顯示了具有 [清單分區] 命令輸出的 [DISKPART] 視窗。 分區 1 和分區 2 顯示在表中。 當分區 1 是所選磁片時，它還顯示 [sel 分區 1] 命令的輸出。](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. 輸入"詳細分區"以檢查分區的狀態。 參見圖 4，其中分區為 *"活動：否*"或圖 5，其中分區為"活動：是"。

   圖 4

   ![圖 4 顯示了 [詳細分區] 命令的輸出的 [DISKPART] 視窗，當分區 1 設置為 [活動：否] 時](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   圖 5

   ![圖 5 顯示了 [磁片部分] 視窗，其中輸出了 [詳細分區] 命令，當分區 1 設置為 [活動：是]時。](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. 如果分區**未處於活動狀態**，請輸入*活動*以更改*活動*標誌。
9. 通過鍵入*詳細資訊分區*，檢查狀態更改是否正確完成。

   圖 6

   ![圖 6 顯示了具有 [詳細分區] 命令輸出的磁片部分視窗，當分區 1 設置為 [活動：是]](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. 輸入*退出*以關閉 DISKPART 工具並保存配置更改。

### <a name="fix-the-disk-partition"></a>修復磁碟分割

1. 打開提升的命令提示符 （cmd.exe）。
2. 使用以下命令在磁片上運行*CHKDSK*並修復錯誤：

   `chkdsk <DRIVE LETTER>: /f`

   添加"/f"命令選項將修復磁片上的任何錯誤。 請確保替換為<DRIVE LETTER>隨附的 OS VHD 的字母。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建議：在重建 VM 之前，請啟用串列主控台和記憶體傾印集合

要啟用記憶體傾印集合和串列主控台，請運行以下腳本：

1. 打開提升的命令提示會話（以管理員身份運行）。
2. 執行下列命令：

   啟用串列主控台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
