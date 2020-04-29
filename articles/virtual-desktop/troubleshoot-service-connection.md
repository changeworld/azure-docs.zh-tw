---
title: 針對服務連接 Windows 虛擬桌面進行疑難排解-Azure
description: 如何解決在 Windows 虛擬桌面租使用者環境中設定用戶端連線時的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127455"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虛擬桌面服務連線

使用本文來解決 Windows 虛擬桌面用戶端連線的問題。

## <a name="provide-feedback"></a>提供意見反應

您可以提供意見反應，並與[Windows 虛擬桌面 Tech 社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)中的產品小組和其他活躍的社區成員討論 Windows 虛擬桌面服務。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連接，但不顯示任何內容（沒有摘要）

使用者可以啟動遠端桌面用戶端，而且能夠進行驗證，不過使用者在 web 探索摘要中看不到任何圖示。

使用下列命令列，確認已將報告問題的使用者指派給應用程式群組：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

確認使用者以正確的認證登入。

如果正在使用 web 用戶端，請確認沒有任何快取的認證問題。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 企業版多會話虛擬機器沒有回應

如果虛擬機器沒有回應，而且您無法透過 RDP 存取它，您必須檢查主機狀態以診斷功能進行疑難排解。

若要檢查主機狀態，請執行此 Cmdlet：

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

如果主機狀態為`NoHeartBeat`，表示 VM 沒有回應，且代理程式無法與 Windows 虛擬桌面服務通訊。

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

您可以執行幾項工作來修正 NoHeartBeat 狀態。

### <a name="update-fslogix"></a>更新 FSLogix

如果您的 FSLogix 不是最新狀態，特別是當它的2.9.7205.27375 版本為 frxdrvvt 時，可能會造成鎖死。 請務必將[FSLogix 更新為最新版本](https://go.microsoft.com/fwlink/?linkid=2084562)。

### <a name="disable-bgtaskregistrationmaintenancetask"></a>停用 BgTaskRegistrationMaintenanceTask

如果更新 FSLogix 無效，問題可能是 BiSrv 元件在每週的維護工作期間耗盡系統資源。 使用下列兩種方法的其中一個來停用 BgTaskRegistrationMaintenanceTask，以暫時停用維護工作：

- 移至 [開始] 功能表，然後搜尋 [**工作排程器**]。 流覽至**工作排程器 Library** > **Microsoft** > **Windows** > **BrokerInfrastructure**。 尋找名為**BgTaskRegistrationMaintenanceTask**的工作。 當您找到它時，請在該檔案上按一下滑鼠右鍵，然後從下拉式功能表中選取 [**停**用]。
- 以系統管理員身分開啟命令列功能表，然後執行下列命令：
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 虛擬桌面和擴大追蹤的總覽，請參閱[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立租使用者和主機集區的問題進行疑難排解，請參閱[建立租使用者和主機集](troubleshoot-set-up-issues.md)區。
- 若要在 Windows 虛擬桌面中設定虛擬機器（VM）時針對問題進行疑難排解，請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要針對搭配 Windows 虛擬桌面使用 PowerShell 時的問題進行疑難排解，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
