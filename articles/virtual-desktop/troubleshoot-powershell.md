---
title: Windows 虛擬桌面電源外殼 - Azure
description: 在設置 Windows 虛擬桌面租戶環境時，如何解決 PowerShell 的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127478"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虛擬桌面 PowerShell

使用本文可以解決在 Windows 虛擬桌面中使用 PowerShell 時的錯誤和問題。 有關遠端桌面服務 PowerShell 的詳細資訊，請參閱[Windows 虛擬桌面電源外殼](/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>在 Windows 虛擬桌面設置期間使用的 PowerShell 命令

本節列出了在設置 Windows 虛擬桌面時通常使用的 PowerShell 命令，並提供瞭解決使用時可能出現的問題的方法。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>錯誤：Add-RdsAppGroupUser 命令 -- 指定的使用者主體名稱已分配給指定主機池中的 RemoteApp 應用組

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因：** 使用的使用者名已分配給不同類型的應用組。 無法將使用者分配給同一工作階段主機池下的遠端桌面和遠端應用組。

**修復：** 如果使用者同時需要遠端應用和遠端桌面，請創建不同的主機池或授予使用者對遠端桌面的存取權限，這將允許在工作階段主機 VM 上使用任何應用程式。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>錯誤：添加 RdsAppGroupUser 命令 -- 與遠端桌面租戶關聯的 Azure 活動目錄中不存在指定的使用者主體名稱

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**原因：**-UserTheName 指定的使用者無法在綁定到 Windows 虛擬桌面租戶的 Azure 活動目錄中找到。

**修復：** 確認以下清單中的專案。

- 使用者同步到 Azure 活動目錄。
- 使用者不與企業 （B2C） 或企業對企業 （B2B） 商務相關聯。
- Windows 虛擬桌面租戶綁定到正確的 Azure 活動目錄。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>錯誤：獲取診斷活動 -- 使用者無權查詢管理服務

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因：** -租戶名稱參數

**修復：** 使用 -租戶名稱\<租戶名稱>發出獲取診斷活動的問題。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>錯誤：獲取診斷活動 -- 使用者無權查詢管理服務

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因：** 使用 -部署開關。

**修復：-** 部署交換器只能由部署管理員使用。 這些管理員通常是遠端桌面服務/Windows 虛擬桌面團隊的成員。 將 -部署開關替換為 -租戶\<名稱租戶名稱>。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>錯誤：新 RdSRole 分配 -- 使用者無權查詢管理服務

**原因 1：** 正在使用的帳戶對租戶沒有遠端桌面服務擁有者許可權。

**修復 1：** 具有遠端桌面服務擁有者許可權的使用者需要執行角色指派。

**原因 2：** 正在使用的帳戶具有遠端桌面服務擁有者許可權，但不是租戶的 Azure 活動目錄的一部分，或者沒有查詢使用者所在的 Azure 活動目錄的許可權。

**修復 2：** 具有活動目錄許可權的使用者需要執行角色指派。

>[!Note]
>New-RdsRole 分配不能向 Azure 活動目錄 （AD） 中不存在的使用者授予許可權。

## <a name="next-steps"></a>後續步驟

- 有關 Windows 虛擬桌面和升級跟蹤的故障排除概述，請參閱[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)。
- 要在 Windows 虛擬桌面環境中創建租戶和主機池時解決問題，請參閱[租戶和主機池創建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虛擬桌面中配置虛擬機器 （VM） 時解決問題，請參閱[工作階段主機虛擬機器配置](troubleshoot-vm-configuration.md)。
- 要解決 Windows 虛擬桌面用戶端連接的問題，請參閱[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 要解決遠端桌面用戶端的問題，請參閱[排除遠端桌面用戶端的故障](troubleshoot-client.md)
- 要瞭解有關該服務的更多資訊，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
- 要完成疑難排解教程，請參閱[教程：解決資源管理器範本部署的疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../azure-resource-manager/templates/deployment-history.md)。
