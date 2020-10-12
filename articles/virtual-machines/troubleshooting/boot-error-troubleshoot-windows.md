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
ms.openlocfilehash: e5ab1262def78da4971ea6e5535f3ac915a38ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526753"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 關機停滯于「正在重新開機」、「正在關機」或「正在停止服務」

本文提供的步驟可解決在 Microsoft Azure 中重新開機 Windows 虛擬機器 (VM) 的「重新開機」、「關機」或「正在停止服務」訊息時，您可能會遇到的問題。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您可能會看到螢幕擷取畫面顯示「重新開機」、「關機」或「正在停止服務」訊息。

![重新開機、關閉和停止服務畫面](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>原因

Windows 會使用關機程式來執行系統維護作業，以及處理變更、角色和功能等變更。 不建議您中斷這個重要的程式，直到它完成為止。 視更新次數和 VM 大小而定，此程式可能需要很長的時間。 如果進程已停止，作業系統可能會損毀。 如果處理常式花費的時間過長，請只中斷進程。

## <a name="solution"></a>解決方法

### <a name="collect-a-process-memory-dump"></a>收集進程記憶體傾印

1. 將 [Procdump 工具](http://download.sysinternals.com/files/Procdump.zip) 下載至新的或現有的資料磁片，並連接到相同區域中的工作 VM。

2. 從工作中的 VM 卸離包含所需檔案的磁片，並將磁片連接到中斷的 VM。 我們會將此磁片呼叫 **公用程式磁片**。

使用 [序列主控台](./serial-console-windows.md) 完成下列步驟：

1. 開啟系統管理 PowerShell，並檢查停止時停止回應的服務。

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 在系統管理 CMD 上，取得沒有回應之服務的 PID。

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. 從沒有回應的進程取得記憶體傾印範例 <STOPPING SERVICE> 。

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 現在終止未回應的進程來解除鎖定關機進程。

   ``
   taskkill /PID <PID> /t /f
   ``

作業系統再次啟動之後，如果正常開機，則只需確定作業系統一致性正常。 如果報告損毀，請執行下列命令，直到磁片無損毀：

``
dism /online /cleanup-image /restorehealth
``

如果您無法收集處理常式記憶體傾印，或此問題是遞迴的，且您需要根本原因分析，請繼續收集下方的 OS 記憶體傾印，繼續開啟支援要求。

### <a name="collect-an-os-memory-dump"></a>收集 OS 記憶體傾印

如果在等待變更之後問題未解決，您就必須收集記憶體傾印檔案並聯系支援人員。 若要收集傾印檔案，請遵循下列步驟：

**將 OS 磁片連結至復原 VM**

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

2. [將 OS 磁片連結至復原 VM](./troubleshoot-recovery-disks-portal-windows.md)。

3. 以遠端桌面連線到復原 VM。

4. 如果 OS 磁片已加密，您必須先關閉加密，再移至下一個步驟。 如需詳細資訊，請參閱 [解密 VM 中無法開機的已加密作業系統磁片](./troubleshoot-bitlocker-boot-error.md#solution)。

**找出傾印檔案，並提交支援票證**

1. 在復原 VM 上，移至已連結 OS 磁碟的 Windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。

2. 找出記憶體 dmp 檔案，然後使用傾印檔案來 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

如果找不到傾印檔案，請移到下一個步驟來啟用傾印記錄檔和序列主控台。

**啟用傾印記錄檔和序列主控台**

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。

2. 執行下列指令碼：

   在此腳本中，我們假設指派給已連結 OS 磁片的磁碟機號是 F。請將它取代為您 VM 中的適當值。

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

3. 確定磁片上有足夠的空間可配置 RAM 的記憶體，這取決於您為此 VM 選取的大小。

4. 如果沒有足夠的空間或 VM 很大 (G、GS 或 E 系列) ，您可以變更將建立此檔案的位置，並將其參考至任何其他連接至 VM 的資料磁片。 若要變更位置，您必須變更下列索引鍵：

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. 卸[離 os 磁片，然後將 os 磁片重新連結至受影響的 VM](./troubleshoot-recovery-disks-portal-windows.md)。

6. 啟動 VM 並存取序列主控台。

7. 選取 [傳送非遮罩式插斷 (NMI) 以觸發記憶體傾印。

   ![傳送非遮罩式插斷](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. 請再次將 OS 磁片連結至復原 VM，並收集傾印檔案。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援服務

收集傾印檔案之後，請洽詢 Microsoft 支援服務以判斷根本原因。
