---
title: 套用原則時，Azure 虛擬機器沒有回應
description: 本文提供的步驟可解決在 Azure VM 中的啟動期間套用原則時，載入畫面沒有回應的問題。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 7160ec9564ede21eab0a205b2d66a7d566639506
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632651"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>套用群組原則本機使用者和群組原則時，VM 沒有回應

本文提供的步驟可解決當 Azure 虛擬機器 (VM) 在啟動期間套用原則時，載入畫面沒有回應的問題。

## <a name="symptoms"></a>徵狀

當您使用 [開機診斷](./boot-diagnostics.md) 來查看 VM 的螢幕擷取畫面時，畫面會停滯載入，並顯示下列訊息：「套用群組原則本機使用者和群組原則」。

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="載入套用群組原則本機使用者與群組原則 (Windows Server 2012 R2) 的螢幕擷取畫面。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="載入套用群組原則本機使用者與群組原則 (Windows Server 2012) 的螢幕擷取畫面。":::

## <a name="cause"></a>原因

當原則嘗試清除舊的使用者設定檔時，有衝突的鎖定。

> [!NOTE]
> 此情況只會在 Windows Server 2012 和 Windows Server 2012 R2 發生。

有問題的原則如下：

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>解決方案

### <a name="process-overview"></a>程序概觀

> [!TIP]
> 如果您有最新的 VM 備份，您可以嘗試 [從備份還原 vm](../../backup/backup-azure-arm-restore-vms.md) 以修正開機問題。

1. [建立和存取修復 VM](#step-1-create-and-access-a-repair-vm)
1. [停用原則](#step-2-disable-the-policy)
1. [啟用序列主控台和記憶體傾印收集](#step-3-enable-serial-console-and-memory-dump-collection)
1. [重建 VM](#step-4-rebuild-the-vm)

> [!NOTE]
> 如果您遇到此開機錯誤，則虛擬作業系統無法運作。 您必須在離線模式中進行疑難排解。

### <a name="step-1-create-and-access-a-repair-vm"></a>步驟 1:建立和存取修復 VM

1. 使用 [VM 修復命令的步驟 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 來準備修復 VM。
2. 使用遠端桌面連線連接到修復 VM。

### <a name="step-2-disable-the-policy"></a>步驟 2:停用原則

1. 在修復 VM 上，開啟 [登錄編輯程式]。
1. 找出 **HKEY_LOCAL_MACHINE** 的金鑰， 然後  >  從功能表中選取 [檔案 **載入 Hive** ]。

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="螢幕擷取畫面，其中顯示 HKEY_LOCAL_MACHINE (已醒目提示的) 與包含載入登錄區的功能表。":::

    - 您可以使用 Load Hive 從離線系統載入登錄機碼。 在此情況下，系統是連接至修復 VM 的已中斷磁片。
    - 全系統的設定會儲存在上 `HKEY_LOCAL_MACHINE` ，而且可以縮寫為 "HKLM"。
1. 在連接的磁碟中，前往 `\windows\system32\config\SOFTWARE` 檔案，並將其開啟。

    1. 當系統提示您輸入名稱時，請輸入 BROKENSOFTWARE。
    1. 若要確認是否已載入 BROKENSOFTWARE，請展開 **HKEY_LOCAL_MACHINE** 並尋找新增的 BROKENSOFTWARE 機碼。
1. 移至 BROKENSOFTWARE，並檢查已載入的 hive 中是否有 Cleanupprofile 機索引鍵。

    1. 如果索引鍵存在，則會設定 Cleanupprofile 機原則。 其值代表以天為單位的保留原則。 繼續刪除機碼。
    1. 如果機碼不存在，則不會設定 CleanupProfile 原則。 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，包含 memory.dmp 檔案，位於已連結 OS 磁碟的 Windows 目錄中。

1. 使用下列命令來刪除 CleanupProfiles 金鑰：

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  使用下列命令來卸載 BROKENSOFTWARE hive：

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>步驟3：啟用序列主控台和記憶體傾印收集

若要啟用記憶體傾印收集和序列主控台，請執行下列腳本：

1. 開啟提升許可權的命令提示字元會話。  (以系統管理員身分執行]。 ) 
1. 執行下列命令以啟用序列主控台：
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. 確認 OS 磁片上的可用空間是否至少等於 VM 的記憶體大小 (RAM) 。

    如果 OS 磁片上沒有足夠的空間，請將記憶體傾印位置變更為連接的資料磁片（具有足夠的可用空間）。 若要變更位置，請將 "% SystemRoot%" 取代為磁碟機號 (例如，在下列命令中使用資料磁片的 "F：" ) 。

    **啟用 OS 傾印的建議設定**

    載入中斷的 OS 磁片：

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    在 ControlSet001 上啟用：
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    在 ControlSet002 上啟用：
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    解除載入中斷的 OS 磁碟：

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>步驟 4：重建 VM

使用 [vm repair 命令的步驟 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 來重組 vm。

如果問題已修正，現在會在本機停用此原則。 若是永久的解決方案，請勿在 Vm 上使用 CleanupProfiles 原則。 使用不同的方法來執行設定檔清除。

請勿使用此原則：

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>後續步驟

如果您在套用 Windows Update 時遇到問題，請參閱套用 [Windows Update 時，VM 沒有回應 "C01A001D" 錯誤](./unresponsive-vm-apply-windows-update.md)。
