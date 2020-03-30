---
title: 在 Azure AD 門戶中查看自訂角色指派 |微軟文檔
description: 現在，您可以在 Azure AD 管理中心中查看和管理 Azure AD 管理員角色的成員。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259704"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>在 Azure 活動目錄中查看自訂角色指派

本文介紹如何查看在 Azure 活動目錄 （Azure AD） 中分配的自訂角色。 在 Azure 活動目錄 （Azure AD） 中，角色可以在組織範圍或單應用程式作用域中分配。

- 組織範圍內的角色指派被添加到單個應用程式角色分配清單中，並且可以在單個應用程式角色分配清單中看到。
- 單個應用程式作用域中的角色指派不會添加到，並且無法在組織範圍分配清單中看到。

## <a name="view-role-assignments-in-the-azure-portal"></a>在 Azure 門戶中查看角色指派

此過程描述查看具有組織範圍的角色的分配。

1. 在 Azure [AD](https://aad.portal.azure.com) 組織中使用特權角色管理員或全域管理員許可權登錄到 Azure AD 管理中心。
1. 選擇**Azure 活動目錄**，選擇**角色和管理員**，然後選擇一個角色來打開它並查看其屬性。
1. 選擇 **"分配"** 以查看角色的分配。

    ![從清單中打開角色時查看角色指派和許可權](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 查看角色指派

本節介紹具有組織範圍的角色的查看分配。 本文使用[Azure 活動目錄 PowerShell 版本 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)模組。 要使用 PowerShell 查看單應用程式作用域分配，可以使用[PowerShell 在"分配自訂角色"中使用](roles-assign-powershell.md)Cmdlet。

### <a name="prepare-powershell"></a>準備電源外殼

首先，必須[下載 Azure AD 預覽 PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/)。

若要安裝 AzureAD PowerShell 模組，請使用下列命令︰

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

若要確認此模組已可使用，請使用下列命令︰

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>查看角色的分配

查看角色指派的示例。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>使用 Microsoft 圖形 API 查看角色指派

本節介紹具有組織範圍的角色的查看分配。  要使用圖形 API 查看單應用程式作用域分配，可以使用圖形 API 在["分配自訂角色"](roles-assign-graph.md)中使用操作。

HTTP 要求獲取給定角色定義的角色指派。

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

回應

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>查看單應用程式作用域的分配

本節介紹具有單應用程式作用域的角色的查看分配。 此功能目前為公開預覽狀態。

1. 在 Azure [AD](https://aad.portal.azure.com) 組織中使用特權角色管理員或全域管理員許可權登錄到 Azure AD 管理中心。
1. 選擇**應用註冊**，然後選擇應用註冊以查看其屬性。 您可能需要選擇 **"所有應用程式"** 才能查看 Azure AD 組織中應用註冊的完整清單。

    ![從應用註冊頁面創建或編輯應用註冊](./media/roles-create-custom/appreg-all-apps.png)

1. 在應用註冊中，選擇**角色和管理員**，然後選擇一個角色以查看其屬性。

    ![從"應用註冊"頁查看應用註冊角色指派](./media/roles-view-assignments/appreg-assignments.png)

1. 選擇 **"分配"** 以查看角色的分配。 從應用註冊中打開分配視圖將顯示限定為此 Azure AD 資源的分配。

    ![從應用註冊的屬性查看應用註冊角色指派](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
