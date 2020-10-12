---
title: Windows 虛擬桌面 (傳統) PowerShell-Azure
description: 當您設定 Windows 虛擬桌面 (傳統) 租使用者環境時，如何針對 PowerShell 的問題進行疑難排解。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 23d1e4b06c9c0278742da0cec8ac565b5f80a362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88004910"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>Windows 虛擬桌面 (傳統) PowerShell

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../troubleshoot-powershell.md)。

使用本文來解決搭配 Windows 虛擬桌面使用 PowerShell 時的錯誤和問題。 如需遠端桌面服務 PowerShell 的詳細資訊，請參閱 [Windows 虛擬桌面 powershell](/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 虛擬桌面設定期間使用的 PowerShell 命令

本節列出設定 Windows 虛擬桌面時通常會使用的 PowerShell 命令，並提供解決使用這些命令時可能發生之問題的方法。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>錯誤： Add-RdsAppGroupUser 命令--指定的 UserPrincipalName 已指派給指定的主機集區中的 RemoteApp 應用程式群組

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因：** 使用的使用者名稱已指派給不同類型的應用程式群組。 無法將使用者指派給相同工作階段主機集區下的遠端桌面和遠端應用程式群組。

**修正：** 如果使用者同時需要遠端應用程式與遠端桌面，請建立不同的主機集區，或將遠端桌面的存取權授與使用者，以允許在工作階段主機 VM 上使用任何應用程式。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>錯誤： Add-RdsAppGroupUser 命令--指定的 UserPrincipalName 不存在於與遠端桌面租使用者相關聯的 Azure Active Directory 中

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**原因：** 在系結至 Windows 虛擬桌面租使用者的 Azure Active Directory 中，找不到-UserPrincipalName 指定的使用者。

**修正：** 確認下列清單中的專案。

- 使用者會同步至 Azure Active Directory。
- 使用者未系結至企業對消費者 (B2C) 或企業對企業 (B2B) 商務。
- Windows 虛擬桌面租使用者已系結至正確的 Azure Active Directory。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>錯誤： Get-RdsDiagnosticActivities--使用者未獲得查詢管理服務的授權

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因：** -TenantName 參數

**修正：** Get-RdsDiagnosticActivities 與-TenantName 的問題 \<TenantName> 。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>錯誤： Get-RdsDiagnosticActivities--使用者未獲得查詢管理服務的授權

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因：** 使用-Deployment 參數。

**修正：** -部署參數只能由部署系統管理員使用。 這些系統管理員通常是遠端桌面服務/Windows 虛擬桌面小組的成員。 以-TenantName 取代-Deployment 參數 \<TenantName> 。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>錯誤： New-RdsRoleAssignment--使用者未獲得查詢管理服務的授權

**原因1：** 使用的帳戶沒有租使用者的遠端桌面服務擁有者許可權。

**修正1：** 具有遠端桌面服務擁有者許可權的使用者必須執行角色指派。

**原因2：** 使用的帳戶有遠端桌面服務擁有者許可權，但不是租使用者 Azure Active Directory 的一部分，或者沒有查詢使用者所在 Azure Active Directory 的許可權。

**修正2：** 具有 Active Directory 許可權的使用者必須執行角色指派。

>[!Note]
>New-RdsRoleAssignment 無法將許可權授與 Azure Active Directory (AD) 中不存在的使用者。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview-2019.md)。
- 若要針對在 Windows 虛擬桌面環境中建立租用戶與主機集區時的問題進行疑難排解，請參閱[租用戶和主機集區建立](troubleshoot-set-up-issues-2019.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration-2019.md)。
- 若要對 Windows 虛擬桌面用戶端連線問題進行疑難排解，請參閱 [Windows 虛擬桌面服務連接](troubleshoot-service-connection-2019.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱 [遠端桌面用戶端疑難排解](../troubleshoot-client.md)
- 若要深入瞭解此服務，請參閱 [Windows 虛擬桌面環境](environment-setup-2019.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../../azure-resource-manager/templates/deployment-history.md)。
