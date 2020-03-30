---
title: Azure 虛擬機器關閉在"重新開機、關閉"或"停止服務"時卡住 |微軟文檔
description: 本文可説明您解決 Azure Windows 虛擬機器中的服務錯誤。
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371367"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 關機停留在"重新開機"、"關閉"或"停止服務"上

本文提供瞭解決在 Microsoft Azure 中重新開機 Windows 虛擬機器 （VM） 時可能會遇到的"重新開機"、"關閉"或"停止服務"消息的步驟。

## <a name="symptoms"></a>徵狀

當您使用[引導診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)來查看 VM 的螢幕截圖時，您可能會看到螢幕截圖顯示消息"重新開機"、"關閉"或"停止服務"。

![重新開機、關閉和停止服務螢幕](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>原因

Windows 使用關機過程執行系統維護操作，並處理更新、角色和功能等更改。 不建議在完成之前中斷此關鍵過程。 根據更新/更改的數量和 VM 大小，該過程可能需要很長時間。 如果進程停止，作業系統可能會損壞。 僅當進程時間過長時才會中斷。

## <a name="solution"></a>解決方法

### <a name="collect-a-process-memory-dump"></a>收集進程記憶體傾印

1. 將[Procdump 工具](http://download.sysinternals.com/files/Procdump.zip)下載到新的或現有的資料磁片中，該磁片從同一區域連接到工作 VM。

2. 從工作 VM 分離包含所需檔的磁片，並將磁片附加到損壞的 VM。 我們稱此磁片為**實用程式磁片**。

使用[串列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)完成以下步驟：

1. 打開管理電源shell 並檢查停止時掛起的服務。

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 在管理 CMD 上，獲取掛起服務的 PID。

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. 從掛起的進程<STOPPING SERVICE>獲取記憶體傾印示例。

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 現在終止掛起的進程以解鎖關機過程。

   ``
   taskkill /PID <PID> /t /f
   ``

作業系統再次啟動後，如果啟動正常，則只需確保作業系統的一致性正常。 如果報告損壞，則運行以下命令，直到磁片無損壞：

``
dism /online /cleanup-image /restorehealth
``

如果您無法收集進程記憶體傾印，或者此問題是遞迴的，並且需要根本原因分析，請繼續收集下面的 OS 記憶體傾印，然後繼續打開支援請求。

### <a name="collect-an-os-memory-dump"></a>收集 OS 記憶體傾印

如果在等待對進程的更改後問題未解決，則需要收集記憶體傾印檔並聯系支援人員。 若要收集傾印檔案，請遵循下列步驟：

**將作業系統磁片連接到恢復 VM**

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)。

2. [將作業系統磁片附加到恢復 VM。](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

3. 以遠端桌面連線到復原 VM。

4. 如果作業系統磁片已加密，則必須在轉到下一步之前關閉加密。 有關詳細資訊，請參閱[解密無法啟動的 VM 中的加密 OS 磁片](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)。

**找出傾印檔案，並提交支援票證**

1. 在復原 VM 上，移至已連結 OS 磁碟的 Windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。

2. 找到記憶體.dmp 檔，然後提交帶有轉儲檔[的支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

如果找不到傾印檔案，請移到下一個步驟來啟用傾印記錄檔和序列主控台。

**啟用傾印記錄檔和序列主控台**

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。

2. 執行下列指令碼：

   在此腳本中，我們假定分配給附加 OS 磁片的磁碟機號為 F. 將其替換為 VM 中的相應值。

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. 驗證磁片上有足夠的空間來分配與 RAM 相同的記憶體，具體取決於您為此 VM 選擇的大小。

4. 如果沒有足夠的空間或 VM 很大（G、GS 或 E 系列），則可以更改將創建此檔的位置，並將其引用到連接到 VM 的任何其他資料磁片。 要更改位置，必須更改以下鍵：

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [分離 OS 磁片，然後將 OS 磁片重新連接到受影響的 VM。](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

6. 啟動 VM 並訪問串列主控台。

7. 選擇"發送不可遮罩中斷 （NMI））"以觸發記憶體傾印。

   ![發送非遮罩式插斷](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. 再次將 OS 磁片連接到恢復 VM，收集轉儲檔。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

收集轉儲檔後，請與 Microsoft 支援部門聯繫以確定根本原因。
