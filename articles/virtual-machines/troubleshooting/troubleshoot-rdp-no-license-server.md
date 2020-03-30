---
title: 當您連線到 Azure VM 時無法使用遠端桌面授權伺服器 | Microsoft Docs
description: 了解如何針對因為沒有可用遠端桌面授權伺服器而導致的 RDP 失敗問題進行疑難排解 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088528"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>當您連線到 Azure VM 時無法使用遠端桌面授權伺服器

本文可協助解決因為沒有提供授權的遠端桌面授權伺服器可以使用，而無法連線到 Azure 虛擬機器 (VM) 的問題。

## <a name="symptoms"></a>徵狀

您在嘗試連線到虛擬機器 (VM) 時遇到下列案例：

- 顯示作業系統已完全載入且正在等待認證的 VM 螢幕擷取畫面。
- 當您嘗試建立 Microsoft 遠端桌面通訊協定 (RDP) 連線時，會收到下列錯誤訊息：

  - 遠端工作階段中斷，因為沒有提供授權的遠端桌面授權伺服器可以使用。

  - 沒有可用的遠端桌面授權伺服器。 遠端桌面服務將停止運作，因為這部電腦已經超過寬限期，而且尚未連線到至少一部有效的 Windows Server 2008 授權伺服器。 請選取這個訊息來開啟 RD 工作階段主機伺服器設定，以便使用授權診斷。

不過，您通常可以使用系統管理工作階段連線到 VM：

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>原因

如果無法使用遠端桌面授權伺服器提供授權來啟動遠端工作階段，就會發生此問題。 即使已在 VM 上設定遠端桌面工作階段主機角色，還是有數個案例會導致此問題：

- 環境中未曾有過遠端桌面授權角色，且已超過 180 天的寬限期。
- 已在環境中安裝遠端桌面授權，但未曾啟動它。
- 環境中的遠端桌面授權並未插入用戶端存取使用權 (CAL) 來設定連線。
- 已在環境中安裝遠端桌面授權。 有可用的 CAL，但並未正確設定它們。
- 遠端桌面授權具有 CAL，且已經啟動。 不過，一些關於遠端桌面授權伺服器的其他問題會導致它無法在環境中提供授權。

## <a name="solution"></a>解決方法

若要解決此問題，請[備份 OS 磁碟](../windows/snapshot-copy-managed-disk.md)並遵循下列步驟：

1. 使用系統管理工作階段連線到 VM：

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    如果您無法使用系統管理工作階段連線到 VM，您可以使用 [Azure 上的虛擬機器序列主控台](serial-console-windows.md)來存取 VM，如下所示：

    1. 選取 [支援與疑難排解]****[序列主控台 (預覽)]**** 來存取序列主控台 > 。 如果已在 VM 上啟用此功能，您就能成功連線該 VM。

    2. 針對 CMD 執行個體建立新通道。 輸入 **CMD** 以啟動通道並取得通道名稱。

    3. 切換至執行 CMD 執行個體的通道。 在此案例中，應該是通道 1：

       ```
       ch -si 1
       ```

    4. 再次選取 **Enter**，然後輸入 VM 的有效使用者名稱和密碼 (本機或網域識別碼)。

2. 檢查 VM 是否已啟用遠端桌面工作階段主機角色。 如果已啟用此角色，請確定它能正確運作。 開啟提升權限的 CMD 執行個體，並遵循下列步驟：

    1. 使用下列命令來檢查遠端桌面工作階段主機角色的狀態：

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        如果此命令所傳回的值為 0，即表示已停用角色，而您可以移至步驟 3。

    2. 使用下列命令來檢查原則並視需要重新設定：

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        如果 **LicensingMode** 值 (針對每個使用者) 已設定為 4 以外的任何其他值，請將該值設定為 4：

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       如果 **SpecifiedLicenseServers** 值不存在，或它具有不正確的授權伺服器資訊，請依下列所示變更它：

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. 當您對登錄進行任何變更之後，請重新啟動 VM。

    4. 如果您沒有 CAL，請移除遠端桌面工作階段主機角色。 然後 RDP 將會回復為正常狀態。 它只會允許兩個針對 VM 的並行 RDP 連線：

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        如果 VM 具有遠端桌面授權角色且沒有使用它，您也可以移除該角色：

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. 確定 VM 可以連線到遠端桌面授權伺服器。 您可以測試 VM 和授權伺服器之間針對連接埠 135 的連線： 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. 如果環境中沒有遠端桌面授權伺服器，但您想要一部，則您可以[安裝遠端桌面授權角色服務](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)) \(英文\)。 然後[設定 RDS 授權](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383) \(英文\)。

4. 如果遠端桌面授權伺服器已設定且狀況良好，請確定會使用 CAL 來啟動遠端桌面授權伺服器。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以解決您的問題。
