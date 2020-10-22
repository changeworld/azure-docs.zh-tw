---
title: PIM 中 Azure AD 角色的 PowerShell-Azure AD |Microsoft Docs
description: 使用 Azure AD Privileged Identity Management (PIM) 中的 PowerShell Cmdlet 來管理 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d04a2941888592ffa37acfe6cba52a33fda528
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92365505"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中 Azure AD 角色的 PowerShell

本文包含使用 Azure Active Directory (Azure AD) PowerShell Cmdlet 來管理 Azure AD Privileged Identity Management PIM (中) 角色的指示。 其中也會說明如何使用 Azure AD PowerShell 模組來完成設定。

> [!Note]
> 只有當您在 Azure AD Privileged Identity Management 的新版本時，才支援我們的官方 PowerShell。 請移至 Privileged Identity Management，並確定您在 [快速啟動] 分頁上有下列橫幅。
> [![檢查您擁有的 Privileged Identity Management 版本](media/pim-how-to-add-role-to-user/pim-new-version.png "選取 Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) 如果您沒有此橫幅，請稍候，我們目前正在推出這項更新後幾周的更新體驗。
> Privileged Identity Management PowerShell Cmdlet 可透過 Azure AD Preview 模組來支援。 如果您使用了不同的模組，而該模組現在會傳回錯誤訊息，請開始使用這個新的模組。 如果您有任何以不同模組為基礎的生產系統，請與您聯繫 [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) 。

## <a name="installation-and-setup"></a>安裝和設定

1. 安裝 Azure AD Preview 模組

    ```powershell
    Install-module AzureADPreview
    ```

1. 在繼續之前，請確定您擁有必要的角色許可權。 如果您嘗試執行管理工作，例如提供角色指派或更新角色設定，請確定您有全域管理員或特殊權限角色管理員角色。 如果您只是想要啟用自己的指派，則不需要預設使用者權力以外的任何許可權。

1. 連線到 Azure AD。

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. 前往**Azure Active Directory**  >  **屬性**  >  **目錄識別碼**，尋找 Azure AD 組織的租使用者識別碼。 在 [Cmdlet] 區段中，每當您需要提供 resourceId 時，請使用此識別碼。

    ![在 Azure AD 組織的屬性中尋找組織識別碼](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 下列各節是可協助您開始執行的簡單範例。 您可以在中找到有關下列 Cmdlet 的詳細檔 [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true](/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true) 。 不過，您必須將 providerID 參數中的 "azureResources" 取代為 "aadRoles"。 您也必須記得將 Azure AD 組織的租使用者識別碼作為 resourceId 參數。

## <a name="retrieving-role-definitions"></a>正在抓取角色定義

使用下列 Cmdlet 來取得 Azure AD 組織中的所有內建和自訂 Azure AD 角色。 這個重要的步驟會提供角色名稱與 roleDefinitionId 之間的對應。 RoleDefinitionId 會在這些 Cmdlet 中使用，以便參考特定的角色。

RoleDefinitionId 僅適用于您的 Azure AD 組織，與角色管理 API 所傳回的 roleDefinitionId 不同。

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

結果：

![取得 Azure AD 組織的所有角色](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>正在抓取角色指派

使用下列 Cmdlet 來取出 Azure AD 組織中的所有角色指派。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

使用下列 Cmdlet 來取得特定使用者的所有角色指派。 這份清單在 Azure AD 入口網站中也稱為「我的角色」。 唯一的差別在於您已為主體識別碼新增篩選。 此內容中的主體識別碼是使用者識別碼或群組識別碼。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

使用下列 Cmdlet 來取得特定角色的所有角色指派。 這裡的 roleDefinitionId 是上一個 Cmdlet 所傳回的識別碼。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

Cmdlet 會產生如下所示的角色指派物件清單。 主體識別碼是指派角色之使用者的使用者識別碼。 指派狀態可能是作用中或符合資格。 如果使用者在使用中，且 LinkedEligibleRoleAssignmentId 欄位中有識別碼，這表示角色目前已啟用。

結果：

![取出 Azure AD 組織的所有角色指派](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>指派角色

使用下列 Cmdlet 來建立合格的指派。

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

排程（定義指派的開始和結束時間）是可建立的物件，如下列範例所示：

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> 如果 endDateTime 的值設定為 null，則表示永久指派。

## <a name="activate-a-role-assignment"></a>啟用角色指派

使用下列 Cmdlet 來啟動合格的指派。

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

這個 Cmdlet 與建立角色指派的 Cmdlet 幾乎完全相同。 指令程式之間的主要差異在於– Type 參數的啟用是 "userAdd"，而不是 "adminAdd"。 另一個差異是– AssignmentState 參數是「作用中」，而不是「合格」。

> [!Note]
> 透過 PowerShell 啟用角色的限制案例有兩種。
> 1. 如果您在角色設定中需要票證系統/票證號碼，則無法提供它們做為參數。 因此，無法啟用 Azure 入口網站以外的角色。 這項功能即將在接下來幾個月推出至 PowerShell。
> 1. 如果您需要進行角色啟用的多重要素驗證，則 PowerShell 目前無法在使用者啟用其角色時，對使用者提出挑戰。 相反地，使用者在連線到 Azure AD 時，必須遵循我們的其中一位工程師的 [這篇 blog 文章](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) 來觸發 MFA 挑戰。 如果您要為 PIM 開發應用程式，其中一個可能的執行是挑戰使用者，並在收到「MfaRule」錯誤之後將其重新連接到模組。

## <a name="retrieving-and-updating-role-settings"></a>正在抓取和更新角色設定

使用下列 Cmdlet 取得 Azure AD 組織中的所有角色設定。

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

設定中有四個主要物件。 PIM 目前只使用其中三個物件。 UserMemberSettings 為啟用設定，AdminEligibleSettings 是合格指派的指派設定，而 AdminmemberSettings 是作用中指派的指派設定。

[![取得和更新角色設定。](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

若要更新角色設定，您必須取得特定角色的現有設定物件，並對其進行變更：

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

然後，您可以將設定套用至特定角色的其中一個物件，如下所示。 這裡的識別碼是可從清單角色設定 Cmdlet 的結果中抓取的角色設定識別碼。

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>後續步驟

- [指派 Azure AD 自訂角色](azure-ad-custom-roles-assign.md)
- [移除或更新 Azure AD 自訂角色指派](azure-ad-custom-roles-update-remove.md)
- [設定 Azure AD 自訂角色指派](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定義](../roles/permissions-reference.md)
