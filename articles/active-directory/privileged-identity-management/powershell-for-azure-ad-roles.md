---
title: PIM 中 Azure AD 角色的 PowerShell-Azure AD |Microsoft Docs
description: 使用 Azure AD Privileged Identity Management （PIM）中的 PowerShell Cmdlet 來管理 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c42c0dd3848ec913f991e4b07612669c5a25c9f1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197275"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中 Azure AD 角色的 PowerShell

本文包含使用 Azure Active Directory （Azure AD） PowerShell Cmdlet 來管理 Privileged Identity Management （PIM）中 Azure AD 角色的指示。 其中也會說明如何使用 Azure AD PowerShell 模組來完成設定。

> [!Note]
> 只有當您在新版本的 Azure AD Privileged Identity Management 時，才支援我們的官方 PowerShell。 請移至 Privileged Identity Management，並確定您在 [快速入門] 分頁上有下列橫幅。
> [![檢查您擁有的 Privileged Identity Management 版本](media/pim-how-to-add-role-to-user/pim-new-version.png "選取 Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)如果您沒有此橫幅，請稍候，我們目前正在推出此更新的體驗，在接下來的幾周內。
> Privileged Identity Management 的 PowerShell Cmdlet 可透過 Azure AD Preview 模組來支援。 如果您已使用不同的模組，而且該模組現在傳回錯誤訊息，請開始使用這個新模組。 如果您有任何以不同模組為基礎的生產系統，請前往pim_preview@microsoft.com

## <a name="installation-and-setup"></a>安裝與設定

1. 安裝 Azure AD 預覽模組

        Install-module AzureADPreview

1. 在繼續之前，請確定您擁有必要的角色許可權。 如果您嘗試執行如提供角色指派或更新角色設定之類的管理工作，請確定您擁有全域管理員或特殊權限角色管理員角色。 如果您只是想要啟用自己的指派，則不需要預設使用者權力以外的許可權。

1. 連線到 Azure AD。

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. 前往**Azure Active Directory**內容  >  **Properties**  >  **目錄識別碼**]，尋找 Azure AD 組織的租使用者識別碼。 當您需要提供 resourceId 時，請在 [Cmdlet] 區段中使用此識別碼。

    ![在 Azure AD 組織的屬性中尋找組織識別碼](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 下列各節是可協助您啟動並執行的簡單範例。 您可以在找到有關下列 Cmdlet 的更詳細檔 https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management 。 不過，您必須將 providerID 參數中的 "azureResources" 取代為 "aadRoles"。 您也必須記得使用您的 Azure AD 組織的 [組織識別碼] 作為 [resourceId] 參數。

## <a name="retrieving-role-definitions"></a>正在抓取角色定義

使用下列 Cmdlet 來取得 Azure AD 組織中的所有內建和自訂 Azure AD 角色。 這個重要步驟會提供角色名稱與 roleDefinitionId 之間的對應。 這些 Cmdlet 會使用 roleDefinitionId 來參考特定的角色。

RoleDefinitionId 是 Azure AD 組織特有的，不同于角色管理 API 所傳回的 roleDefinitionId。

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

結果：

![取得 Azure AD 組織的所有角色](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>正在抓取角色指派

使用下列 Cmdlet 來取出 Azure AD 組織中的所有角色指派。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

使用下列 Cmdlet 來抓取特定使用者的所有角色指派。 在 Azure AD 入口網站中，這份清單也稱為「我的角色」。 唯一的差異在於您已新增主體識別碼的篩選準則。 此內容中的主體識別碼是使用者識別碼或群組識別碼。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

使用下列 Cmdlet 來取得特定角色的所有角色指派。 此處的 roleDefinitionId 是上一個 Cmdlet 所傳回的識別碼。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Cmdlet 會產生如下所示的角色指派物件清單。 主體識別碼是指派角色之使用者的使用者識別碼。 指派狀態可能是 [作用中] 或 [符合資格]。 如果使用者在使用中，且 [LinkedEligibleRoleAssignmentId] 欄位中有一個識別碼，表示角色目前已啟用。

結果：

![取得 Azure AD 組織的所有角色指派](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>指派角色

使用下列 Cmdlet 來建立合格的指派。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

排程（定義指派的開始和結束時間）是可以建立的物件，如下列範例所示：

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> 如果 endDateTime 的值設為 null，則表示永久指派。

## <a name="activate-a-role-assignment"></a>啟動角色指派

使用下列 Cmdlet 來啟動合格的指派。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

此 Cmdlet 與建立角色指派的 Cmdlet 幾乎完全相同。 Cmdlet 的主要差異在於– Type 參數的啟用是 "userAdd"，而不是 "adminAdd"。 另一個差異是– AssignmentState 參數是 "Active"，而不是「合格」。

> [!Note]
> 透過 PowerShell 進行角色啟用有兩種限制案例。
> 1. 如果您的角色設定需要票證系統/票證號碼，則沒有任何方法可將其當做參數提供。 因此，無法啟動 Azure 入口網站以外的角色。 這項功能即將在接下來幾個月推出至 PowerShell。
> 1. 如果您需要多重要素驗證來啟用角色，則 PowerShell 目前無法在啟動其角色時挑戰使用者。 相反地，當使用者從我們的其中一位工程師，遵循[此 blog 文章](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)連線到 Azure AD 時，將需要觸發 MFA 挑戰。 如果您要開發適用于 PIM 的應用程式，其中一種可能的執行方式是在收到「MfaRule」錯誤之後，挑戰使用者並將其重新連接至模組。

## <a name="retrieving-and-updating-role-settings"></a>正在抓取和更新角色設定

使用下列 Cmdlet 來取得 Azure AD 組織中的所有角色設定。

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

設定中有四個主要物件。 PIM 目前只會使用其中三個物件。 UserMemberSettings 是啟用設定，AdminEligibleSettings 是合格指派的指派設定，而 AdminmemberSettings 是作用中指派的指派設定。

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

若要更新角色設定，您必須取得特定角色的現有設定物件，並對其進行變更：

    $setting = Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "roleDefinitionId eq 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'"
    $setting.UserMemberSetting.justificationRule = '{"required":false}'

接著，您可以將設定套用至特定角色的其中一個物件，如下所示。 此處的識別碼是角色設定識別碼，可從清單角色設定 Cmdlet 的結果中抓取。

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>後續步驟

- [指派 Azure AD 自訂角色](azure-ad-custom-roles-assign.md)
- [移除或更新 Azure AD 自訂角色指派](azure-ad-custom-roles-update-remove.md)
- [設定 Azure AD 自訂角色指派](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定義](../users-groups-roles/directory-assign-admin-roles.md)
