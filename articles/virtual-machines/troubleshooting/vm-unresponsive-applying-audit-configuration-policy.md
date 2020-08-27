---
title: 套用稽核原則設定原則時，虛擬機器沒有回應
description: 本文提供的步驟可解決在套用稽核原則設定原則時，虛擬機器 (VM) 變得沒有回應的問題，這會導致 Azure VM 無法開機。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: e55fa377f28572901202b4d722bea70786edae22
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942081"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>套用稽核原則設定原則時，虛擬機器沒有回應

本文提供的步驟可解決在套用稽核原則設定原則時，虛擬機器 (VM) 變得沒有回應的問題，這會導致 Azure VM 無法開機。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示作業系統 (OS) 在開機期間具有套用 **稽核原則設定原則**的訊息。

  ![OS 開機時，會顯示下列訊息：「套用稽核原則設定原則」](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Windows Server 2012 中的 OS 開機，並顯示下列訊息：「套用稽核原則設定原則」](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>原因

當原則嘗試清除舊的使用者設定檔時，有衝突的鎖定。

> [!NOTE]
> 此情況只會在 Windows Server 2012 和 Windows Server 2012 R2 發生。

以下是有問題的原則： *Computer 設定 \ 管理 Templates \ 系統/使用者 Profiles\Delete 使用者設定檔，比系統重新開機時指定的天數還舊。*

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀

1. 建立及存取修復 VM。
1. 停用原則。
1. 啟用序列主控台與記憶體傾印收集。
1. 重建 VM。
1. 收集記憶體傾印檔案並提交支援票證。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 使用 [VM 修復命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。
1. 使用遠端桌面連線，連線至修復 VM。

### <a name="disable-the-policy"></a>停用原則

1. 在修復 VM 上，開啟 [ **登錄編輯程式**]。
1. 找出 **HKEY_LOCAL_MACHINE** 的金鑰，然後從功能表中選取 [檔案] **> 載入 Hive** ]。

   ![在登錄編輯器中流覽以載入 hive。](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - 您可以使用 Load Hive 從離線系統載入登錄機碼。 在此情況下，系統是連接至修復 VM 的已中斷磁片。
   - 全系統的設定會儲存在 **HKEY_LOCAL_MACHINE** 上，並且可以縮寫為 **HKLM**。

1. 在連接的磁片中，開啟檔案 `\windows\system32\config\SOFTWARE` 。

   - 當系統提示您輸入名稱時，請輸入 **BROKENSOFTWARE**。
   - 若要確認是否已載入 **BROKENSOFTWARE** ，請展開 [ **HKEY_LOCAL_MACHINE** ]，然後尋找新增的 **BROKENSOFTWARE** 索引鍵。

1. 移至 **BROKENSOFTWARE** ，並檢查已載入的 hive 中是否有 **CleanupProfiles** 索引鍵。

   - 如果索引鍵存在，則會設定 **CleanupProfiles** 原則。 其值代表以天為單位的保留原則。
   - 如果機碼不存在，則不會設定 **CleanupProfiles** 原則。 在此情況下，請直接跳到以 [記憶體傾印檔案提交支援票證](#collect-the-memory-dump-file-and-submit-a-support-ticket)。

1. 使用下列命令來刪除 **CleanupProfiles** 金鑰：

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. 使用下列命令來卸載 **BROKENSOFTWARE** hive：

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>啟用序列主控台與記憶體傾印收集

**建議**：重建 VM 之前，請先透過執行下列指令碼來啟用序列主控台與記憶體傾印收集：

1. 以系統管理員身分開啟提升權限的命令提示字元工作階段。
1. 列出 BCD 存放區資料，並判斷您將在下一個步驟中使用的開機載入器識別碼。

   1. 針對第1代 VM，請輸入下列命令，並記下列出的識別碼：

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - 在命令中， `<BOOT PARTITON>` 將取代為連接磁片中包含開機資料夾之磁碟分割的字母。

        ![[圖 4] 顯示在第1代 VM 中列出 BCD 存放區的輸出，這些 VM 會列示在 Windows 開機載入器的識別碼號碼。](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. 針對第2代 VM，請輸入下列命令，並記下列出的識別碼：

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - 在命令中，以 `<LETTER OF THE EFI SYSTEM PARTITION>` EFI 系統磁碟分割的字母取代。
      - 啟動磁片管理主控台來識別標示為 **EFI 系統磁碟分割**的適當系統磁碟分割可能很有説明。
      - 識別碼可以是唯一的 GUID，也可以是預設的 **bootmgr**。

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
   
### <a name="rebuild-the-virtual-machine"></a>重建虛擬機器

1. 使用 [VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)\(部分機器翻譯\)重建 VM。

1. 測試您的 VM 是否正常開機，以查看問題是否已修正。

   - 如果問題尚未修正，請繼續收集傾印檔案， [並提交支援票證](#collect-the-memory-dump-file-and-submit-a-support-ticket)。
   - 如果已修正問題，則不需要採取進一步的步驟。

如果問題已修正，現在會在本機停用此原則。 若是永久的解決方案，請勿在 Vm 上使用 CleanupProfiles 原則，因為它會自動刪除使用者設定檔。 使用不同的方法來執行設定檔清除，例如已排程的工作或腳本。

**請勿使用此原則：** 
*在系統重新開機時，Machine\Admin Templates\System\User Profiles\Delete 使用者設定檔的時間超過指定的天數。*

### <a name="the-issue-should-now-be-fixed"></a>現在應該已修正此問題

測試您的 VM，以確保其運作正常。 如果您仍遇到問題，則可以繼續進行下一節，以取得進一步的協助。

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>收集記憶體傾印檔案並提交支援票證

若要解決此問題，您需要先收集損毀的記憶體傾印檔案，然後再以記憶體傾印檔案的支援來聯絡。 若要收集傾印檔案，請遵循下列步驟：

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>將 OS 磁片連結至新的修復 VM

1. 使用 [VM Repair 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 的步驟1-3 來準備新的修復 VM。
1. 使用遠端桌面連線連線至修復 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案並提交支援票證

1. 在修復 VM 上，移至連接的 OS 磁片中的 [windows] 資料夾。 如果指派給已連結 OS 磁片的磁碟機號標示為 *F*，則您必須移至 `F:\Windows` 。
1. 找出檔案 `memory.dmp` ，然後使用記憶體傾印檔案 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。
1. 如果您在尋找檔案時遇到問題 `memory.dmp` ，請改為 [在序列主控台中使用非遮罩式插斷 (NMI) 呼叫](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) 。 遵循本指南，在 [這裡使用 NMI 呼叫來產生損毀](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)傾印檔案。
