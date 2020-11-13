---
title: 在 Azure Active Directory 入口網站中查看自訂角色指派 |Microsoft Docs
description: 您現在可以在 Azure Active Directory 系統管理中心查看並管理 Azure Active Directory 系統管理員角色的成員。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9f7c6ec0917adbca5b44feee1ec285f4337ac4c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579023"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>使用 Azure Active Directory 查看自訂角色指派

本文說明如何在 Azure Active Directory (Azure AD) 中，查看您所指派的自訂角色。 在 Azure Active Directory (Azure AD) 中，可以在整個組織範圍或單一應用程式範圍中指派角色。

- 在全組織範圍中的角色指派會新增至，並可在單一應用程式角色指派清單中看到。
- 在單一應用程式範圍中的角色指派不會新增至，而且無法在整個組織範圍的指派清單中看到。

## <a name="view-role-assignments-in-the-azure-portal"></a>查看 Azure 入口網站中的角色指派

此程式描述如何在整個組織範圍內查看角色指派。

1. 使用 Azure AD 組織的特殊權限角色管理員或全域系統管理員許可權登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 **Azure Active Directory** 、選取 [ **角色和系統管理員** ]，然後選取要開啟的角色，並查看其屬性。
1. 選取 [ **指派** ] 以查看角色的指派。

    ![從清單中開啟角色時查看角色指派和許可權](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 來查看角色指派

本節說明如何在整個組織範圍內查看角色的指派。 本文使用 [Azure Active Directory PowerShell 第2版](/powershell/module/azuread/?view=azureadps-2.0#directory_roles) 模組。 若要使用 PowerShell 來查看單一應用程式範圍指派，您可以在使用 [Powershell 指派自訂角色](custom-assign-powershell.md)時使用 Cmdlet。

### <a name="prepare-powershell"></a>準備 PowerShell

首先，您必須 [下載 Azure AD Preview PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/)。

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

### <a name="view-the-assignments-of-a-role"></a>查看角色的指派

查看角色指派的範例。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>使用 Microsoft Graph API 來查看角色指派

本節說明如何在整個組織範圍內查看角色的指派。  若要使用圖形 API 來查看單一應用程式範圍指派，您可以使用圖形 API 中的作業 [指派自訂角色](custom-assign-graph.md)。

用來取得指定角色定義角色指派的 HTTP 要求。

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

## <a name="view-assignments-of-single-application-scope"></a>查看單一應用程式範圍的指派

本節說明如何透過單一應用程式範圍來查看角色的指派。 此功能目前為公開預覽狀態。

1. 使用 Azure AD 組織的特殊權限角色管理員或全域系統管理員許可權登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 **應用程式註冊** ，然後選取 [應用程式註冊] 以查看其屬性。 您可能必須選取 [所有應用程式] 才能查看 Azure AD 組織中的完整應用程式註冊清單。

    ![從應用程式註冊頁面建立或編輯應用程式註冊](./media/view-assignments/app-reg-all-apps.png)

1. 在應用程式註冊中，選取 [ **角色和系統管理員** ]，然後選取要查看其屬性的角色。

    ![從應用程式註冊頁面查看應用程式註冊角色指派](./media/view-assignments/app-reg-assignments.png)

1. 選取 [ **指派** ] 以查看角色的指派。 從應用程式註冊中開啟指派視圖，會顯示範圍設定為此 Azure AD 資源的指派。

    ![從應用程式註冊的屬性查看應用程式註冊角色指派](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](permissions-reference.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。