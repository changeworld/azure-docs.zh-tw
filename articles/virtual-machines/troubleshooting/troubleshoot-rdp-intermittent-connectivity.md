---
title: Azure VM 中的遠端桌面時常斷線 | Microsoft Docs
description: 了解如何針對 Azure VM 中時常斷線的遠端桌面進行疑難排解。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: eec52fb2342ee85932a6e24aa4cb292496beccad
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087294"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Azure VM 中的遠端桌面時常斷線

本文將說明如何透過遠端桌面通訊協定 (RDP) 對 Azure 虛擬機器 (VM) 的時常斷線情況進行疑難排解。


## <a name="symptom"></a>徵狀

您在工作階段期間遇到間歇性的 RDP 連線中斷問題。 您一開始可以連線到 VM，但之後連線就斷了。

## <a name="cause"></a>原因

如果 RDP 接聽程式的設定不正確，就可能發生此問題。 一般而言，此問題會發生在使用自訂映像的 VM 上。

## <a name="solution"></a>解決方案

在您遵循這些步驟之前，請將受影響 VM 的[OS 磁片快照](../windows/snapshot-copy-managed-disk.md)集作為備份。 

若要針對此問題進行疑難排解，請使用「序列控制項」，或藉由將 VM 的 OS 磁碟連結至復原 VM 來[修復離線的 VM](#repair-the-vm-offline)。

### <a name="serial-control"></a>序列控制項

1. 連線至[序列主控台並開啟 CMD 執行個體](./serial-console-windows.md)。 然後，執行下列命令來重設 RDP 組態。 如果 VM 上未啟用序列主控台，請移至下一個步驟。
2. 將 RDP 安全性階層降低至 0。 在此設定上，伺服器和用戶端之間的通訊會使用原生 RDP 加密。

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

3. 將加密層級調降至最低設定，以允許舊版 RDP 用戶端進行連線。

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

4. 將 RDP 設定為可載入用戶端電腦的使用者設定。

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

5. 啟用 RDP 的「持續連線」控制項：

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

6. 設定 RDP 的重新連線控制項：

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

7. 設定 RDP 的工作階段時間控制項：

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

8. 設定 RDP 的中斷連線時間控制項： 

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f` `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

9. 設定 RDP 的連線時間控制項：

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

10. 設定 RDP 的工作階段閒置時間控制項：

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f`

11. 設定「限制並行連線數目上限」控制項：

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f`

12. 重新啟動 VM，並使用 RDP 再次嘗試連線。

### <a name="repair-the-vm-offline"></a>修復離線的 VM

1. [將 OS 磁片連結至復原 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 在 OS 磁碟連結至復原 VM 後，請確定該磁碟在磁碟管理主控台中標示為 [線上]****。 記下指派給已連結 OS 磁碟的磁碟機代號。
3. 在您連接的 OS 磁碟上，瀏覽至 **\windows\system32\config** 資料夾。 複製此資料夾中的所有檔案作為備份，以在需要復原時使用。
4. 啟動 [登錄編輯程式] (regedit.exe)。
5. 選取 [HKEY_LOCAL_MACHINE]**** 機碼。 在功能表上 **，選取 [** 檔案] [  >  **載入 Hive**]：
6. 流覽至您連接的 OS 磁片上的**\windows\system32\config\SYSTEM**資料夾。 針對 Hive 的名稱，輸入 **BROKENSYSTEM**。 新的 Hive 會顯示在 [HKEY_LOCAL_MACHINE]**** 機碼下方。 載入 **HKEY_LOCAL_MACHINE** 機碼下的軟體 hive **\windows\system32\config\SOFTWARE**。 針對 Hive 軟體的名稱，輸入 **BROKENSOFTWARE**。 
7. 開啟提升權限的命令提示字元視窗 (**以系統管理員身分執行**)，並執行其餘步驟中的命令，以重設 RDP 組態。 
8. 將 RDP 安全性階層降低至 0，好讓伺服器和用戶端之間的通訊會使用原生 RDP 加密：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

9. 將加密層級調降至最低設定，以允許舊版 RDP 用戶端進行連線：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

10. 將 RDP 設定為可載入用戶端機器的使用者設定。

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

11. 啟用 RDP 的「持續連線」控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

12. 設定 RDP 的重新連線控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

13. 設定 RDP 的工作階段時間控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

14. 設定 RDP 的中斷連線時間控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

15. 設定 RDP 的連線時間控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

16. 設定 RDP 的工作階段閒置時間控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

17. 設定「限制並行連線數目上限」控制項：

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

18. 重新啟動 VM，並使用 RDP 再次嘗試連線。

## <a name="need-help"></a>需要協助嗎？ 
請連絡支援人員。 如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。





