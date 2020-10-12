---
title: 從 Azure Active Directory 中的群組移除角色指派 |Microsoft Docs
description: 預覽用來委派身分識別管理的自訂 Azure AD 角色。 在 Azure 入口網站、PowerShell 或圖形 API 中管理 Azure 角色。
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
ms.openlocfilehash: 15312159bc0487f7d03c06fa947f69b1f6f9600c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513395"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>在 Azure Active Directory 中從群組移除角色指派

本文說明 IT 系統管理員如何移除指派給群組 Azure AD 角色。 在 Azure 入口網站中，您現在可以移除使用者的直接與間接角色指派。 如果群組成員資格將角色指派給使用者，請從群組中移除使用者，以移除角色指派。

## <a name="using-azure-admin-center"></a>使用 Azure 系統管理中心

1. 使用 Azure AD 組織的特殊權限角色管理員或全域系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。

1. 選取**角色和系統管理員**  >  ***角色名稱***。

1. 選取您要從中移除角色指派的群組，然後選取 [ **移除指派**]。

   ![從選取的群組中移除角色指派。](./media/roles-groups-remove-assignment/remove-assignment.png)

1. 當系統要求您確認您的動作時，請選取 **[是]**。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>建立可指派給角色的群組

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>取得您想要指派群組的角色定義

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>建立角色指派

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>移除角色指派

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>建立可指派 Azure AD 角色的群組

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
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

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>建立角色指派

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>刪除角色指派

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>接下來的步驟

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [對指派給雲端群組的角色進行疑難排解](roles-groups-faq-troubleshooting.md)
