---
title: 使用管理單位作用域(預覽)分配和列出角色 - Azure 活動目錄 |微軟文件
description: 使用管理單位限制 Azure 活動目錄中的角色分配範圍
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870423"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>將範圍角色分配給管理單位

在 Azure 活動目錄 (Azure AD) 中,可以將使用者分配給範圍僅限於一個或多個管理單位 (A) 的 Azure AD 角色,以便進行更精細的管理控制。

有關準備使用 PowerShell 和 Microsoft 圖形進行管理單元管理的步驟,請參閱[入門](roles-admin-units-manage.md#get-started)。

## <a name="roles-available"></a>可用角色

角色  |  描述
----- |  -----------
驗證系統管理員  |  只能查看、設置和重置分配管理單元中的任何非管理員使用者的任何非管理員使用者身份驗證方法資訊。
組管理員  |  只能管理組和組設置的所有方面,如指定管理單元中的命名和過期策略。
服務台系統管理員  |  只能重置分配管理單元中非管理員和幫助台管理員的密碼。
授權管理員  |  只能在管理單位內分配、刪除和更新許可證分配。
密碼管理員  |  只能重置分配管理單元中非管理員和密碼管理員的密碼。
使用者管理員  |  可以管理使用者和組的所有方面,包括僅為分配的管理單元中的有限管理員重置密碼。

## <a name="assign-a-scoped-role"></a>配置範圍角色

### <a name="azure-portal"></a>Azure 入口網站

跳到**Azure AD >门户中的管理单位**。 選擇要將角色分配給使用者的管理單位。 在左側窗格中,選擇角色和管理員以列出所有可用角色。

![選擇管理單位來改變角色範圍](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

選擇要分配的角色,然後選擇 **「添加分配**」 。 這將在右側滑動打開面板,您可以在其中選擇要分配給角色的一個或多個使用者。

![選擇要範圍的角色,然後選擇"添加分配"](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

突出顯示的部分可以根據需要更改特定環境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>在 AU 上列出作用域管理員

### <a name="azure-portal"></a>Azure 入口網站

使用管理單位作用域完成的所有角色分配都可以在 Azure AD[的管理單位部分](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中查看。 跳到**Azure AD >门户中的管理单位**。 選擇要列出的角色分配的管理單位。 選擇**角色和管理員**並打開角色以查看管理單元中的分配。

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

突出顯示的部分可以根據需要更改特定環境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>後續步驟

- [管理單位故障排除和常見問題解答](roles-admin-units-faq-troubleshoot.md)
