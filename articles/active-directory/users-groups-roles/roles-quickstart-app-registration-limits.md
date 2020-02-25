---
title: 移除建立應用程式註冊的限制-Azure AD |Microsoft Docs
description: 指派自訂角色，以在 Azure AD 中授與不受限制的應用程式註冊 Active Directory
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559040"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>快速入門：授與建立無限制應用程式註冊的許可權

在本快速入門中，您將建立自訂角色，其具有建立不限數目的應用程式註冊的許可權，然後將該角色指派給使用者。 然後，指派的使用者可以使用 Azure AD 入口網站、Azure AD PowerShell 或 Microsoft Graph API 來建立應用程式註冊。 與內建應用程式開發人員角色不同的是，這個自訂角色會授與建立不限數目之應用程式註冊的能力。 應用程式開發人員角色會授與能力，但已建立的物件總數限制為250，以防止達到全[目錄的物件配額](directory-service-limits-restrictions.md)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisite"></a>必要條件

建立和指派 Azure AD 自訂角色所需的最低特殊許可權角色是特殊許可權角色管理員。

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>使用 Azure AD 入口網站建立新的自訂角色

1. 以特殊許可權角色管理員或 Azure AD 組織中的全域管理員許可權登入 [Azure AD 系統管理中心](https://aad.portal.azure.com) 。
1. 選取 [ **Azure Active Directory**]，選取 [**角色和系統管理員**]，然後選取 [**新增自訂角色**]。

    ![從 [角色和系統管理員] 頁面建立或編輯角色](./media/roles-create-custom/new-custom-role.png)

1. 在 [**基本**] 索引標籤上，為角色的名稱提供「應用程式註冊建立者」，並在角色描述中輸入「可建立無限數量的應用程式註冊」，然後選取 **[下一步]** 。

    ![在 [基本] 索引標籤上提供自訂角色的名稱和描述](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. 在 [**許可權**] 索引標籤的 [搜尋] 方塊中，輸入 "microsoft. directory/applications/create"，然後選取所需許可權旁的核取方塊，然後選取 [**下一步]** 。

    ![在 [許可權] 索引標籤上選取自訂角色的許可權](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. 在 [**審查 + 建立**] 索引標籤上，檢查許可權，然後選取 [**建立**]。

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>使用 Azure AD 入口網站將角色指派給使用者

1. 使用您 Azure AD 組織中特殊權限角色管理員或全域管理員的許可權，登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 [ **Azure Active Directory** ]，然後選取 [**角色和系統管理員**]。
1. 選取 [應用程式註冊建立者] 角色，然後選取 [**新增指派**]。
1. 選取所需的使用者，然後按一下 [**選取**] 將使用者新增至角色。

完成！ 在本快速入門中，您已成功建立自訂角色，且其許可權可建立不限數目的應用程式註冊，然後將該角色指派給使用者。

> [!TIP]
> 若要使用 Azure AD 入口網站將角色指派給應用程式，請在 [指派] 頁面的 [搜尋] 方塊中，輸入應用程式的名稱。 應用程式預設不會顯示在清單中，但會在搜尋結果中傳回。

### <a name="app-registration-permissions"></a>應用程式註冊許可權

有兩種許可權可以授與建立應用程式註冊的能力，每個都有不同的行為。

- microsoft 目錄/應用程式/createAsOwner：指派此許可權會導致建立者新增為所建立應用程式註冊的第一個擁有者，而所建立的應用程式註冊將計入建立者的250建立物件配額中。
- microsoft directory/applicationPolicies/create：指派此許可權會導致建立者不會被新增為所建立應用程式註冊的第一個擁有者，而建立的應用程式註冊將不會算在建立者的250建立物件配額中。 請謹慎使用此許可權，因為在達到目錄層級配額之前，不會讓受託人建立應用程式註冊。 如果同時指派這兩個許可權，則會優先使用此許可權。

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 建立自訂角色

使用下列 PowerShell 腳本建立新的角色：

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 指派自訂角色

#### <a name="prepare-powershell"></a>準備 PowerShell

首先，從[PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 然後使用下列命令匯入 Azure AD PowerShell 預覽模組：

```powershell
import-module azureadpreview
```

若要確認模組已備妥可供使用，請將下列命令所傳回的版本與此處所列的版本進行比對：

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>指派自訂角色

使用下列 PowerShell 腳本指派角色：

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>使用 Microsoft Graph API 建立自訂角色

建立自訂角色的 HTTP 要求。

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

body

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>使用 Microsoft Graph API 指派自訂角色

角色指派結合了安全性主體識別碼（可以是使用者或服務主體）、角色定義（角色）識別碼，以及 Azure AD 資源範圍。

指派自訂角色的 HTTP 要求。

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

body

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>後續步驟

- 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
- 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
