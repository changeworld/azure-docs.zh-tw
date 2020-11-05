---
title: 指派和列出具有管理單位範圍的角色-Azure Active Directory |Microsoft Docs
description: 使用管理單位來限制 Azure Active Directory 中的角色指派範圍。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ed2741c7dd754127a57642703b650a70637c63
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393433"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>將限域角色指派給管理單位

在 Azure Active Directory (Azure AD) ，如需更細微的系統管理控制，您可以將使用者指派至範圍限制為一或多個系統管理單位的 Azure AD 角色。

若要準備使用 PowerShell 和 Microsoft Graph 來管理管理單位，請參閱 [開始](admin-units-manage.md#get-started)使用。

## <a name="available-roles"></a>可用的角色

角色  |  描述
----- |  -----------
驗證系統管理員  |  只能針對指派的管理單位中的任何非系統管理員使用者，查看、設定及重設驗證方法資訊。
群組管理員  |  只能在指派的管理單位中管理群組和群組設定的所有層面，例如命名和到期原則。
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

您可以使用 Azure 入口網站、PowerShell 或 Microsoft Graph 來指派範圍角色。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 Azure 入口網站中，移至 **Azure AD** 。

1. 選取 [ **管理單位** ]，然後選取您要指派使用者角色範圍的管理單位。 

1. 在左窗格中，選取 [ **角色和系統管理員** ] 來列出所有可用的角色。

   ![[角色和系統管理員] 窗格的螢幕擷取畫面，用來選取您要指派其角色範圍的管理單位。](./media/admin-units-assign-roles/select-role-to-scope.png)

1. 選取要指派的角色，然後選取 [ **新增指派** ]。 

1. 在 [ **新增指派** ] 窗格中，選取要指派給角色的一或多個使用者。

   ![選取要設定領域的角色，然後選取 [新增指派]](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> 若要使用 Azure AD Privileged Identity Management (PIM) 在管理單位上指派角色，請參閱 [在 pim 中指派 Azure AD 角色](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)。

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

您可以視需要變更特定環境的醒目提示區段。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

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

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>在管理單位中查看已設定範圍的系統管理員清單

您可以使用 Azure 入口網站、PowerShell 或 Microsoft Graph 來查看已設定範圍的系統管理員清單。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

您可以在 Azure AD 的 [ [管理單位] 區段](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中，查看以管理單位範圍建立的所有角色指派。 

1. 在 Azure 入口網站中，移至 **Azure AD** 。

1. 在左窗格中，選取 [ **管理單位** ]，然後選取您想要查看的角色指派清單的管理單位。 

1. 選取 [ **角色和系統管理員** ]，然後開啟角色以在管理單位中查看指派。

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

您可以視需要變更特定環境的醒目提示區段。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](groups-concept.md)
- [針對指派給雲端群組的角色進行疑難排解](groups-faq-troubleshooting.md)
