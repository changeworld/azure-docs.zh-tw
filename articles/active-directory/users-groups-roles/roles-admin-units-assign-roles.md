---
title: 指派和列出具有系統管理單位範圍的角色（預覽）-Azure Active Directory |Microsoft Docs
description: 使用管理單位來限制 Azure Active Directory 中的角色指派範圍
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 918675b111b7b1b85669692b63fed683ea2831f8
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475629"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>將限域角色指派給管理單位

在 Azure Active Directory （Azure AD）中，您可以將使用者指派給範圍限制為一或多個管理單位（au）的 Azure AD 角色，以進行更細微的系統管理控制。

如需準備使用 PowerShell 和 Microsoft Graph 進行管理單位管理的步驟，請參閱[快速入門](roles-admin-units-manage.md#get-started)。

## <a name="roles-available"></a>可用的角色

角色  |  描述
----- |  -----------
驗證系統管理員  |  只有在指派的管理單位中，才有權查看、設定及重設任何非系統管理員使用者的驗證方法資訊。
群組管理員  |  只能管理群組和群組設定的所有層面，例如指派的管理單位中的命名和到期原則。
服務台系統管理員  |  只能在指派的管理單位中，重設非系統管理員和技術服務人員系統管理員的密碼。
授權管理員  |  只能在管理單位中指派、移除及更新授權指派。
密碼管理員  |  只能在指派的管理單位內重設非系統管理員和密碼管理員的密碼。
使用者管理員  |  可管理使用者和群組的所有層面，包括僅在指派的管理單位內重設受限系統管理員的密碼。

## <a name="assign-a-scoped-role"></a>指派已設定範圍的角色

### <a name="azure-portal"></a>Azure 入口網站

在入口網站中，移至 **Azure AD > 管理單位**。 選取您要將角色指派給使用者的管理單位。 在左窗格中選取 [角色和系統管理員]，以列出所有可用的角色。

![選取要變更角色範圍的管理單位](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

選取要指派的角色，然後選取 [**新增指派**]。 右側會開啟一個面板，您可以在其中選取一或多個要指派給角色的使用者。

![選取要設定範圍的角色，然後選取 [新增指派]](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>列出 AU 上的限域系統管理員

### <a name="azure-portal"></a>Azure 入口網站

使用管理單位範圍完成的所有角色指派，都可以在 Azure AD 的 [[管理單位] 區段](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中看到。 在入口網站中，移至 **Azure AD > 管理單位**。 選取您想要列出之角色指派的管理單位。 選取 [**角色和系統管理員**]，並開啟角色以在管理單位中查看指派。

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [針對指派給雲端群組的角色進行疑難排解](roles-groups-faq-troubleshooting.md)
