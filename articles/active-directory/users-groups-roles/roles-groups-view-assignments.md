---
title: 在 Azure Active Directory 中查看指派給群組的角色 |Microsoft Docs
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475979"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>在 Azure Active Directory 中查看指派給群組的角色

本節說明如何使用 Azure AD 系統管理中心來查看指派給群組的角色。 [群組] 和 [已指派角色] 是預設的使用者許可權。

1. 使用任何非系統管理員或系統管理員認證登入[Azure AD 系統管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。

1. 選取您感興趣的群組。

1. 選取 [**指派的角色**]。 您現在可以看到所有指派給這個群組的 Azure AD 角色。

   ![查看指派給所選群組的所有角色](./media/roles-groups-view-assignments/view-assignments.png)

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

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [針對指派給雲端群組的角色進行疑難排解](roles-groups-faq-troubleshooting.md)
