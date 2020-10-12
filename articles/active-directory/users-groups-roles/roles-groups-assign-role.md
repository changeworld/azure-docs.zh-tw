---
title: 在 Azure Active Directory 中將角色指派給雲端群組 |Microsoft Docs
description: 將 Azure AD 角色指派給 Azure 入口網站、PowerShell 或圖形 API 中角色可指派的群組。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b5709369dfc2025b55bc3acec69e9328ce403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513412"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>在 Azure Active Directory 中將角色指派給雲端群組

本節說明 IT 系統管理員如何將 Azure Active Directory (Azure AD) 角色指派給 Azure AD 群組。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 系統管理中心

將群組指派給 Azure AD 角色類似于指派使用者和服務主體，但只能使用可指派角色的群組。 在 Azure 入口網站中，只會顯示可指派角色的群組。

1. 使用 Azure AD 組織的特殊權限角色管理員或全域系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。

1. 選取**Azure Active Directory**  >  **角色和系統管理員**，然後選取您想要指派的角色。

1. 在 [ ***角色名稱*** ] 頁面上，選取 [> **新增指派**]。

   ![新增角色指派](./media/roles-groups-assign-role/add-assignment.png)

1. 選取群組。 只會顯示可指派給 Azure AD 角色的群組。

    [![針對新的角色指派，只會顯示可指派的群組。](media/roles-groups-assign-role/eligible-groups.png "針對新的角色指派，只會顯示可指派的群組。")](media/roles-groups-assign-role/eligible-groups.png#lightbox)

1. 選取 [新增]。

如需指派角色許可權的詳細資訊，請參閱 [將系統管理員和非系統管理員角色指派給使用者](../fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>建立可指派給角色的群組

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>取得您想要指派之角色的角色定義

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>建立角色指派

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>建立可指派 Azure AD 角色的群組

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>取得角色定義

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>建立角色指派

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [對指派給雲端群組的角色進行疑難排解](roles-groups-faq-troubleshooting.md)
