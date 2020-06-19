---
title: 套用原則時，Azure 虛擬機器沒有回應
description: 本文提供的步驟可解決在 Azure VM 開機期間套用原則時，載入畫面停滯的問題。
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
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748727"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>套用「群組原則本機使用者與群組」原則時，VM 沒有回應

本文會提供步驟以解決在 Azure VM 開機期間套用原則時，載入畫面停滯的問題。

## <a name="symptoms"></a>徵狀

使用[開機診斷](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)檢視 VM 的螢幕擷取畫面時，畫面會在載入時停滯並顯示下列訊息：「正在套用群組原則本機使用者與群組原則」。

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="載入套用群組原則本機使用者與群組原則 (Windows Server 2012 R2) 的螢幕擷取畫面。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="載入套用群組原則本機使用者與群組原則 (Windows Server 2012) 的螢幕擷取畫面。":::

## <a name="cause"></a>原因

當原則嘗試清除舊的使用者設定檔時，會因為發生衝突而鎖定。

> [!NOTE]
> 此情況只會在 Windows Server 2012 和 Windows Server 2012 R2 發生。

有問題的原則如下：

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>解決方案

### <a name="process-overview"></a>程序概觀

1. [建立和存取修復 VM](#step-1-create-and-access-a-repair-vm)
2. [停用原則](#step-2-disable-the-policy)
3. [啟用序列主控台和記憶體傾印集合](#step-3-enable-serial-console-and-memory-dump-collection)
4. [重建 VM](#step-4-rebuild-the-vm)

> [!NOTE]
> 若遇到此開機錯誤，則客體 OS 將無法運作。 您必須在離線模式中進行疑難排解。

### <a name="step-1-create-and-access-a-repair-vm"></a>步驟 1:建立和存取修復 VM

1. 使用 [VM 修復命令的步驟 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 來準備修復 VM。
2. 使用遠端桌面連線，連線至修復 VM。

### <a name="step-2-disable-the-policy"></a>步驟 2:停用原則

1. 在修復 VM 上，開啟 [登錄編輯程式]。
2. 找出 **HKEY_LOCAL_MACHINE** 機碼，然後從功能表中選取 [檔案] > [載入登錄區...]。

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="螢幕擷取畫面，其中顯示 HKEY_LOCAL_MACHINE (已醒目提示的) 與包含載入登錄區的功能表。":::

    - [載入登錄區] 可供從離線系統載入登錄機碼，在本案例中，會將中斷的磁碟連接至修復 VM。
    - 全系統的設定會儲存在 `HKEY_LOCAL_MACHINE` 上，並會縮寫為 "HKLM"。
3. 在連接的磁碟中，前往 `\windows\system32\config\SOFTWARE` 檔案，並將其開啟。

    1. 系統會提示輸入名稱， 輸入 BROKENSOFTWARE。<br/>
    2. 若要確認是否已載入 BROKENSOFTWARE，請展開 **HKEY_LOCAL_MACHINE** 並尋找新增的 BROKENSOFTWARE 機碼。
4. 巡覽至 BROKENSOFTWARE，並檢查 CleanupProfile 機碼是否存在於載入的登錄區中。

    1. 如果該機碼存在，則會設定 CleanupProfile 原則，其值代表保留原則的天數。 繼續刪除機碼。<br/>
    2. 如果機碼不存在，則不會設定 CleanupProfile 原則。 [提交支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，包含 memory.dmp 檔案，位於已連結 OS 磁碟的 Windows 目錄中。

5. 使用此命令刪除 CleanupProfiles 機碼：

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  若要解除載入 BROKENSOFTWARE 登錄區，請使用下列命令：

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>步驟 3：啟用序列主控台和記憶體傾印集合

若要啟用記憶體傾印集合和序列主控台，請執行下列指令碼：

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。
2. 執行以下命令：

    **啟用序列主控台**： 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. 確認 OS 磁碟上的可用空間是否高於 VM 的記憶體大小 (RAM)。

    如果 OS 磁碟上沒有足夠的空間，請變更記憶體傾印的位置，然後將其轉接至具有足夠空間的已連接資料磁碟上。 若要變更位置，請在下列命令中使用資料磁碟的磁碟機代號 (例如 "F:")取代 "%SystemRoot%"。

    **啟用 OS 傾印的建議設定**：

    載入中斷的 OS 磁碟：

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

使用 [VM 修復命令的步驟 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 重新組裝 VM。

如果問題已修正，則會在本機停用此原則。 請勿在 VM 上使用 CleanupProfiles，即可永久解決此問題。 使用不同的方法來執行設定檔清除。

請勿使用此原則：

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>後續步驟

如果在套用 Windows Update 時遇到問題，請參閱[套用 Windows Update 時，VM 沒有回應並顯示 "C01A001D" 錯誤](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update) (機器翻譯)。