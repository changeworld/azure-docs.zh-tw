---
title: PIM 中 Azure AD 角色的電源外殼 - Azure AD |微軟文件
description: 在 Azure AD 特權識別管理 (PIM) 中使用 PowerShell cmdlet 管理 Azure AD 角色。
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
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519657"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>特權識別管理中 Azure AD 角色的 PowerShell

本文包含有關使用 Azure 活動目錄 (Azure AD) PowerShell cmdlet 在特權識別管理 (PIM) 中管理 Azure AD 角色的說明。 其中也會說明如何使用 Azure AD PowerShell 模組來完成設定。

> [!Note]
> 僅當您使用新版本的 Azure AD 特權標識管理時,我們的官方 PowerShell 才受支援。 請轉到特權標識管理,並確保快速入門邊欄選項卡上具有以下橫幅。
> [![檢查您擁有的權限身分管理版本](media/pim-how-to-add-role-to-user/pim-new-version.png "選擇 Azure AD >特权标识管理")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)如果您沒有此橫幅,請稍候,因為我們目前正在在未來幾周內推出此更新的體驗。
> 通過 Azure AD 預覽模組支援特權標識管理 PowerShell cmdlet。 如果您一直在使用其他模組,並且該模組現在返回一條錯誤消息,請開始使用此新模組。 如果您有其他模組之上建構的任何生產系統,請聯絡pim_preview@microsoft.com

## <a name="installation-and-setup"></a>安裝與設定

1. 安裝 Azure AD 預覽模組

        Install-module AzureADPreview

1. 在繼續操作之前,請確保您具有所需的角色許可權。 如果您嘗試執行管理任務,如授予角色分配或更新角色設置,請確保具有全域管理員或特權角色管理員角色管理員角色。 如果您只是嘗試啟動自己的分配,則不需要超出默認使用者許可權的許可權。

1. 連線到 Azure AD。

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. 通過訪問**Azure 活動目錄** > **屬性** > 目錄 ID 查找租戶**ID。** 在 cmdlet 部分中,每當需要提供資源 Id 時,都會使用此 ID。

    ![在 Azure AD 組織屬性中尋找租戶 ID](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 以下各節是可説明您啟動和運行的簡單示例。 您可以在 中找到有關 以下 cmdlethttps://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management的更多詳細資訊 文件。 但是,您需要將提供程式 ID 參數中的「azureResources」替換為「aadRole」。。 您還需要記住將 Azure AD 組織的租戶 ID 用作 resourceId 參數。

## <a name="retrieving-role-definitions"></a>檢索角色定義

使用以下 cmdlet 獲取 Azure AD 組織(租戶)中的所有內置和自定義 Azure AD 角色。 這一重要步驟為您提供了角色名稱和角色定義 Id 之間的映射。 角色定義Id用於這些 cmdlet,以便引用特定角色。

角色定義Id特定於 Azure AD 組織,與角色管理 API 返回的角色定義Id 不同。

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

結果：

![取得 Azure AD 組織的所有角色](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>檢索角色分配

使用以下 cmdlet 檢索 Azure AD 組織中的所有角色分配。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

使用以下 cmdlet 檢索特定使用者的所有角色分配。 此清單在 Azure AD 門戶中也稱為「我的角色」。 這裡唯一的區別是您為主題 ID 添加了篩選器。 此上下文中的主題 ID 是使用者 ID 或組 ID。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

使用以下 cmdlet 檢索特定角色的所有角色分配。 此處的角色定義 Id 是上一個 cmdlet 傳回的 ID。

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

cmdlet 會導致下面顯示的角色分配物件清單。 主題識別碼是角色分配給的使用者的使用者 ID。 分配狀態可以是活動狀態,也可以符合條件。 如果用戶處於活動狀態,並且"連結合格角色分配Id"欄位中存在ID,則表示角色當前已啟動。

結果：

![檢索 Azure AD 組織的所有角色分配](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>指派角色

使用以下 cmdlet 創建符合條件的分配。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

定義分配開始和結束時間的計劃是一個可以創建的物件,如下所示:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> 如果 endDateTime 的值設定為 null,則表示永久分配。

## <a name="activate-a-role-assignment"></a>啟動角色配置

使用以下 cmdlet 啟動符合條件的分配。

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

此 cmdlet 與建立角色分配的 cmdlet 幾乎相同。 cmdlet 之間的主要區別是,對於 _Type 參數,啟動是"userAdd"而不是"adminAdd"。 另一個區別是 _AssignmentState 參數為"活動"而不是"合格」。。

> [!Note]
> 通過 PowerShell 進行角色啟動有兩種限制方案。
> 1. 如果您在角色設置中需要票證系統/票證編號,則無法將這些號碼作為參數提供。 因此,無法在 Azure 門戶之外啟動角色。 此功能將在未來幾個月內推廣到 PowerShell。
> 1. 如果需要多重身份驗證才能啟動角色,PowerShell 目前無法在使用者啟動其角色時對其提出質疑。 相反,使用者在連接到 Azure AD 時需要通過關注我們的工程師的[博客文章](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)來觸發 MFA 挑戰。 如果您正在開發 PIM 應用,一個可能的實現是向使用者發起挑戰,並在使用者收到"MfaRule"錯誤後將其重新連接到模組。

## <a name="retrieving-and-updating-role-settings"></a>檢索和更新角色設定

使用以下 cmdlet 獲取 Azure AD 組織中的所有角色設置。

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

設置中有四個主要物件。 PIM 目前僅使用其中三個物件。 "用戶成員設置"是啟動設置,"管理員資格設置"是符合條件的分配分配設置,管理員成員設置是活動分配的分配設置。

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

要更新角色設置,您需要首先定義設置物件,如下所示:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

然後,您可以繼續將設置應用於特定角色的物件之一,如下所示。 此處的 ID 是從清單角色設定 cmdlet 的結果中檢索的角色設定 ID。

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>後續步驟

- [指派 Azure AD 自訂角色](azure-ad-custom-roles-assign.md)
- [移除或更新 Azure AD 自訂角色指派](azure-ad-custom-roles-update-remove.md)
- [設定 Azure AD 自訂角色指派](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定義](../users-groups-roles/directory-assign-admin-roles.md)
