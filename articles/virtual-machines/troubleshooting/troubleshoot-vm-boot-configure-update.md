---
title: VM 啟動在 Azure 中停滯在「Windows 正在就緒。 請勿關閉電腦。」| Microsoft Docs
description: 介紹出現以下情況時，所應採取的疑難排解步驟：VM 啟動停滯在「正在準備 Windows， 請勿關閉電腦。」
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 415895b894261ade9b2332eb3fb926eba74fe937
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078403"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 啟動在 Azure 中停滯在「Windows 正在就緒。 請勿關閉電腦」的狀態

本文說明當您在 Microsoft Azure 中啟動 Windows 虛擬機器（VM）時，您可能會遇到的「準備就緒」和「正在取得 Windows 就緒」畫面。 它提供步驟，以協助您收集支援票證的資料。

 

## <a name="symptoms"></a>徵狀

Windows VM 不會開機。 當您使用 [**開機診斷**] 取得 VM 的螢幕擷取畫面時，您可能會看到 vm 顯示「準備就緒」或「正在取得 Windows 就緒」訊息。

![Windows Server 2012 R2 的訊息範例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![訊息範例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

這個問題通常發生在伺服器正在進行設定變更之後的最後重新啟動。 設定變更可能是由 Windows 更新或伺服器的角色/功能變更起始。 對於 Windows Update，如果更新的大小較大，作業系統會需要更多時間設定變更。

## <a name="collect-an-os-memory-dump"></a>收集 OS 記憶體傾印

如果在等候變更之後無法解決問題，您就必須收集記憶體傾印檔案並聯絡支援。 若要收集傾印檔案，請遵循下列步驟：

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. 擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。
2. [將 OS 磁片連結至復原 VM](../windows/troubleshoot-recovery-disks-portal.md)。
3. 以遠端桌面連線到復原 VM。 
4. 如果 OS 磁片已加密，您必須先關閉加密，再移至下一個步驟。 如需詳細資訊，請參閱在[無法開機的 VM 中解密加密的 OS 磁片](troubleshoot-bitlocker-boot-error.md#solution)。

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>找出傾印檔案，並提交支援票證

1. 在復原 VM 上，移至已連結 OS 磁碟的 Windows 資料夾。 如果指派給已連結 OS 磁碟的磁碟機代號是 F，您必須移至 F:\Windows。
2. 找出記憶體 dmp 檔案，然後提交包含傾印檔案的[支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

如果找不到傾印檔案，請移到下一個步驟來啟用傾印記錄檔和序列主控台。

### <a name="enable-dump-log-and-serial-console"></a>啟用傾印記錄檔和序列主控台

若要啟用傾印記錄檔與序列主控台，請執行下列指令碼。

1. 開啟提升權限的命令提示字元工作階段 (以系統管理員身分執行)。
2. 執行下列指令碼：

    在此指令碼中，我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將它取代為您 VM 中的適當值。

    ```powershell
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
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. 確定磁碟上有足夠的空間可配置和 RAM 一樣多的記憶體，這取決於您為此 VM 選取的大小。
    2. 如果沒有足夠的空間，或這是大型 VM (G、GS 或 E 系列)，則您可以變更此檔案將建立的位置，並將它參考至連結到 VM 的其他任何資料磁碟。 若要這樣做，您必須變更下列機碼：
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [卸離 OS 磁碟，然後將 OS 磁碟重新連結至受影響的 VM](../windows/troubleshoot-recovery-disks-portal.md)。
4. 啟動 VM 並存取序列主控台。
5. 選取 **[傳送非遮罩式插斷（NMI）** ] 以觸發記憶體傾印。
    ![有關在何處傳送不可遮罩中斷的影像](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. 再次將 OS 磁片連結至復原 VM，並收集傾印檔案。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

收集傾印檔案之後，請連絡 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以分析根本原因。