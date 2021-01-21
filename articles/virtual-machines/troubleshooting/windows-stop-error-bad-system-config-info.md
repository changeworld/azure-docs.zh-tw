---
title: Windows 停止錯誤-0x00000074 錯誤的系統組態資訊
description: 本文提供的步驟可解決 Windows 無法開機的問題，而且需要重新開機，因為 Azure 虛擬機器 (VM) 中的系統設定資訊不正確。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: cbfdb9a73f53e194b43010c0b2d84357aa3e2e5b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631980"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows 停止錯誤-0x00000074 錯誤的系統組態資訊

本文提供的步驟可解決 Windows 無法開機的問題，而且需要重新開機，因為 Azure 虛擬機器 (VM) 中的系統設定資訊不正確。

## <a name="symptom"></a>徵狀

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，您會看到螢幕擷取畫面顯示 Windows 停止程式碼 **#0x00000074** 或 **BAD_SYSTEM_CONFIG_INFO**。

*您的電腦遇到問題，需要重新開機。您可以重新開機。* 
*如需此問題和可能修正的詳細資訊， http://windows.com/stopcode 請造訪* 
*如果您致電支援人員，請提供下列資訊：* 
*停止程式碼： BAD_SYSTEM_CONFIG_INFO*

  ![Windows stop code 0x00000074，也會顯示為「BAD_SYSTEM_CONFIG_INFO」。 Windows 會通知使用者其電腦已發生問題，需要重新開機。](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>原因

如果 **系統** 註冊表 hive 似乎已損毀，就會發生 **BAD_SYSTEM_CONFIG_INFO** 停止程式碼。 此錯誤可能是由下列任何原因所造成：

- 登錄 hive 未正確關閉。
- 登錄區已損毀。
- 有遺失的登錄機碼或值。

## <a name="solution"></a>解決方法

### <a name="process-overview"></a>程序概觀：

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

1. 建立及存取修復 VM。
1. 檢查 hive 是否損毀。
1. 啟用序列主控台與記憶體傾印收集。
1. 重建 VM。

> [!NOTE]
> 遇到此錯誤時，客體作業系統 (OS) 無法運作。 您將會在離線模式中進行疑難排解以解決此問題。

### <a name="create-and-access-a-repair-vm"></a>建立及存取修復 VM

1. 使用 [VM 修復命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) \(部分機器翻譯\) 的步驟 1-3 準備修復 VM。
1. 檢查 hive 是否損毀。
1. 使用遠端桌面連線連接到修復 VM。
1. 複製 `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` 資料夾，並將它儲存在您狀況良好的磁碟分割或另一個安全的位置。 因為您將編輯重要的登錄檔，所以請將此資料夾備份為預防措施。 

> [!NOTE]
> `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config`如果您需要復原您對登錄所做的任何變更，請複製資料夾的複本作為備份。

### <a name="check-for-hive-corruption"></a>檢查 hive 損毀

下列指示將協助您判斷原因是因為 hive 損毀，或 hive 未正確關閉。 如果 hive 未正確關閉，您將能夠解除鎖定檔案並修正您的 VM。

1. 在修復 VM 上，開啟 [ **登錄編輯程式** ] 應用程式。 在 Windows 搜尋列中輸入 "REGEDIT" 來尋找它。
1. 在 [登錄編輯程式] 中，選取 **HKEY_LOCAL_MACHINE** 將其反白顯示，然後選取 [檔案] **> [載入 Hive ...** ]。 。
1. 流覽 `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` 並選取 [ **開啟**]。
1. 當系統提示您輸入名稱時，請輸入 **BROKENSYSTEM**。

   1. 如果 hive 無法開啟，或如果是空的，則 hive 已損毀。 如果 hive 已損毀，請 [開啟支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

     ![發生錯誤，指出登錄編輯程式無法載入 hive。](./media/windows-stop-error-bad-system-config-info/2.png)

   1. 如果 hive 正常開啟，則 hive 未正確關閉。 繼續進行步驟5。

1. 若要修正未正確關閉的 hive，請反白顯示 **BROKENSYSTEM** ，然後選取 **[檔案 > 卸載 hive ...** ] 解除鎖定檔案。

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

使用 [VM 修復命令的步驟 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)\(部分機器翻譯\)重建 VM。