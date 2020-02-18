---
title: Azure 虛擬機器關機停滯于重新開機、關閉或停止服務 |Microsoft Docs
description: 本文可協助您針對 Azure Windows 虛擬機器中的服務錯誤進行疑難排解。
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
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371367"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 關機停滯于 [重新開機]、[正在關閉] 或 [停止服務]

本文提供解決在 Microsoft Azure 重新開機 Windows 虛擬機器（VM）時，可能會遇到「重新開機」、「正在關閉」或「停止服務」訊息之問題的步驟。

## <a name="symptoms"></a>徵狀

當您使用 [[開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)] 來查看 VM 的螢幕擷取畫面時，您可能會看到螢幕擷取畫面顯示「重新開機」、「正在關閉」或「停止服務」訊息。

![重新開機、關閉和停止服務畫面](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>原因

Windows 使用關機程式來執行系統維護作業，以及處理更新、角色和功能等變更。 不建議您中斷此重要程式，直到它完成為止。 視更新/變更的數目和 VM 大小而定，此程式可能需要很長的時間。 如果進程已停止，作業系統可能會損毀。 只有在進程耗費時間過長時，才會中斷程式。

## <a name="solution"></a>解決方法

### <a name="collect-a-process-memory-dump"></a>收集處理常式記憶體傾印

1. 將[Procdump 工具](http://download.sysinternals.com/files/Procdump.zip)下載至新的或現有的資料磁片，並將其連接至相同區域中的工作中 VM。

2. 卸離包含工作中 VM 所需檔案的磁片，並將磁片連結至中斷的 VM。 我們正將此磁片呼叫到**公用程式磁片**。

使用[序列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)來完成下列步驟：

1. 開啟系統管理 Powershell，並檢查停止時暫停的服務。

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 在系統管理 CMD 上，取得無回應服務的 PID。

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. 從無回應的進程 <STOPPING SERVICE>取得記憶體傾印範例。

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 現在會終止停止回應程式，以解除鎖定關機進程。

   ``
   taskkill /PID <PID> /t /f
   ``

當 OS 再次啟動時，如果它正常開機，則只需確定作業系統一致性是正常的。 如果報告損毀，請執行下列命令，直到磁片無損毀為止：

``
dism /online /cleanup-image /restorehealth
``

如果您無法收集處理常式記憶體傾印，或此問題是遞迴的，而且您需要根本原因分析，請在下方收集 OS 記憶體傾印，繼續開啟支援要求。

### <a name="collect-an-os-memory-dump"></a>收集 OS 記憶體傾印

如果在等候變更之後無法解決問題，您就必須收集記憶體傾印檔案並聯絡支援。 若要收集傾印檔案，請遵循下列步驟：

**將 OS 磁片連結至復原 VM**

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)。

2. [將 OS 磁碟連結至復原 VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)。

3. 以遠端桌面連線到復原 VM。

4. 如果 OS 磁片已加密，您必須先關閉加密，再移至下一個步驟。 如需詳細資訊，請參閱在[無法開機的 VM 中解密加密的 OS 磁片](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)。

**找出傾印檔案並提交支援票證**

1. 在復原 VM 上，移至已連結 OS 磁碟的 Windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。

2. 找出記憶體 dmp 檔案，然後提交包含傾印檔案的[支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

如果找不到傾印檔案，請移到下一個步驟來啟用傾印記錄檔和序列主控台。

**啟用傾印記錄檔和序列主控台**

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。

2. 執行下列指令碼：

   在此腳本中，我們假設指派給所連結 OS 磁片的磁碟機號是 F。請將它取代為您 VM 中的適當值。

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

3. 請確認磁片上有足夠的空間可配置 RAM，視您為此 VM 選取的大小而定。

4. 如果沒有足夠的空間或 VM 很大（G、GS 或 E 系列），您可以變更此檔案的建立位置，並將其指向連結至 VM 的任何其他資料磁片。 若要變更位置，您必須變更下列機碼：

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. 卸[離 os 磁片，然後將 os 磁片重新附加至受影響的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)。

6. 啟動 VM 並存取序列主控台。

7. 選取 [傳送非遮罩式插斷（NMI）] 以觸發記憶體傾印。

   ![傳送非遮罩式插斷](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. 再次將 OS 磁片連結至復原 VM，並收集傾印檔案。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

收集傾印檔案之後，請洽詢 Microsoft 支援服務以判斷根本原因。
