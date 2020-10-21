---
title: 指派和列出具有管理單位範圍的角色-Azure Active Directory |Microsoft Docs
description: 使用管理單位來限制 Azure Active Directory 中的角色指派範圍
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d60d803fb73a0f299b00179e5f598f3189e03e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309659"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>將限域角色指派給管理單位

在 Azure Active Directory (Azure AD) 中，您可以將使用者指派至範圍限制為一或多個系統管理單位的 Azure AD 角色 (au) 以進行更細微的系統管理控制。

如需準備使用 PowerShell 和 Microsoft Graph 進行管理單位管理的步驟，請參閱[快速入門](roles-admin-units-manage.md#get-started)。

## <a name="roles-available"></a>可用的角色

角色  |  描述
----- |  -----------
驗證系統管理員  |  只能針對指派的管理單位中的任何非系統管理員使用者，查看、設定及重設驗證方法資訊。
群組管理員  |  可以管理群組和群組設定的所有層面，例如僅限指派的管理單位中的命名和到期原則。
服務台系統管理員  |  只能針對指派的管理單位重設非系統管理員和技術支援管理員的密碼。
授權管理員  |  只能指派、移除及更新管理單位中的授權指派。
密碼管理員  |  只能針對指派的管理單位內的非系統管理員和密碼系統管理員重設密碼。
使用者管理員  |  可以管理使用者和群組的所有層面，包括只在指派的管理單位內為受限的系統管理員重設密碼。

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>可以指派給限域角色的安全性主體

您可以將下列安全性主體指派給具有管理單位範圍的角色：

* 使用者
* 角色可指派的雲端群組 (預覽) 
* 服務主體名稱 (SPN)

## <a name="assign-a-scoped-role"></a>指派範圍角色

### <a name="azure-portal"></a>Azure 入口網站

在入口網站中，移至 **Azure AD > 管理單位**。 選取您要將角色指派給使用者的管理單位。 在左窗格中，選取 [角色和系統管理員] 來列出所有可用的角色。

![選取管理單位來變更角色範圍](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

選取要指派的角色，然後選取 [ **新增指派**]。 面板隨即開啟，您可以在其中選取一或多個要指派給角色的使用者。

![選取要設定領域的角色，然後選取 [新增指派]](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> 若要使用 PIM 在管理單位上指派角色，請遵循 [此處](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)的步驟。

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>列出 AU 上已設定範圍的系統管理員

### <a name="azure-portal"></a>Azure 入口網站

您可以在 Azure AD 的 [ [管理單位] 區段](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中，查看使用管理單位範圍完成的所有角色指派。 在入口網站中，移至 **Azure AD > 管理單位**。 選取您要列出之角色指派的管理單位。 選取 **角色和系統管理員** ，並開啟角色以在管理單位中查看指派。

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [對指派給雲端群組的角色進行疑難排解](roles-groups-faq-troubleshooting.md)