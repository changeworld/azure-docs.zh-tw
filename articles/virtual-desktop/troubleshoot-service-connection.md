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
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612634"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虛擬桌面服務連線

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager Windows 虛擬桌面物件的春季2020更新。 如果您使用的是 Windows 虛擬桌面不含 Azure Resource Manager 物件的2019版，請參閱[這篇文章](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)。
>
> Windows 虛擬桌面春季2020更新目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議針對生產環境工作負載使用。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用本文來解決 Windows 虛擬桌面用戶端連線的問題。

## <a name="provide-feedback"></a>提供意見反應

您可以提供意見反應，並與[Windows 虛擬桌面 Tech 社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)中的產品小組和其他活躍的社區成員討論 Windows 虛擬桌面服務。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連接，但不顯示任何內容（沒有摘要）

使用者可以啟動遠端桌面用戶端，而且能夠進行驗證，不過使用者在 web 探索摘要中看不到任何圖示。

使用下列命令列，確認已將報告問題的使用者指派給應用程式群組：

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

確認使用者以正確的認證登入。

如果正在使用 web 用戶端，請確認沒有任何快取的認證問題。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 企業版多會話虛擬機器沒有回應

如果虛擬機器沒有回應，而且您無法透過 RDP 存取它，您必須檢查主機狀態以診斷功能進行疑難排解。

若要檢查主機狀態，請執行此 Cmdlet：

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

如果主機狀態為`NoHeartBeat`，表示 VM 沒有回應，且代理程式無法與 Windows 虛擬桌面服務通訊。

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

您可以執行幾項工作來修正 NoHeartBeat 狀態。

### <a name="update-fslogix"></a>更新 FSLogix

如果您的 FSLogix 不是最新狀態，特別是當它的2.9.7205.27375 版本為 frxdrvvt 時，可能會造成鎖死。 請務必將[FSLogix 更新為最新版本](https://go.microsoft.com/fwlink/?linkid=2084562)。

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 虛擬桌面和擴大追蹤的總覽，請參閱[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立 Windows 虛擬桌面環境和主機集區的問題進行疑難排解，請參閱[環境和主機集區建立](troubleshoot-set-up-issues.md)。
- 若要在 Windows 虛擬桌面中設定虛擬機器（VM）時針對問題進行疑難排解，請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要針對搭配 Windows 虛擬桌面使用 PowerShell 時的問題進行疑難排解，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
