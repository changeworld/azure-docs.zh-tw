---
title: 在 Azure Active Directory 中查看指派給群組的角色 |Microsoft Docs
description: 瞭解如何使用 Azure AD 系統管理中心來查看指派給群組的角色。 查看群組和指派的角色是預設的使用者權限。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1939be42126606fdae261e60c890c71374c894
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741820"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>在 Azure Active Directory 中查看指派給群組的角色

本節說明如何使用 Azure AD 系統管理中心來查看指派給群組的角色。 查看群組和指派的角色是預設的使用者權限。

1. 使用任何非系統管理員或系統管理員認證登入 [Azure AD admin center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。

1. 選取您感興趣的群組。

1. 選取 [ **指派的角色**]。 您現在可以看到指派給此群組的所有 Azure AD 角色。

   ![查看指派給所選群組的所有角色](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>使用 PowerShell

### <a name="get-object-id-of-the-group"></a>取得群組的物件識別碼

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>查看群組的角色指派

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>取得群組的物件識別碼

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>取得群組的角色指派

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](groups-concept.md)
- [對指派給雲端群組的角色進行疑難排解](groups-faq-troubleshooting.md)
