---
title: 移除建立應用程式註冊的限制 - Azure AD | Microsoft Docs
description: 在 Azure AD Active Directory 中指派自訂角色以授與無限制應用程式註冊
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 260b0621bf204c6d7593b3696c602665c42ad77e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740851"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>快速入門：授與建立無限制應用程式註冊的權限

在本快速入門指南中，您將建立自訂角色，並使其有權建立不限數目的應用程式註冊，然後將該角色指派給使用者。 然後，指派的使用者可使用 Azure AD 入口網站、Azure AD PowerShell 或 Microsoft Graph API 來建立應用程式註冊。 與內建應用程式開發人員角色不同，此自訂角色會授與建立不限數目之應用程式註冊的能力。 應用程式開發人員角色會授與此能力，但可建立的物件總數限制為 250 個，以防止達到[整個目錄範圍的物件配額](../enterprise-users/directory-service-limits-restrictions.md)。 建立和指派 Azure AD 自訂角色所需的最低特殊權限角色，是特殊權限角色管理員。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>使用 Azure AD 入口網站建立自訂角色

1. 使用 Azure AD 組織中的特殊權限角色管理員或全域管理員權限登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [Azure Active Directory]，選取 [角色和系統管理員]，然後選取 [新增自訂角色]。

    ![從 [角色和系統管理員] 頁面建立或編輯角色](./media/quickstart-app-registration-limits/new-custom-role.png)

1. 在 [基本] 索引標籤上，提供「應用程式註冊建立者」作為角色的名稱，並提供「可建立不限數目的應用程式註冊」作為角色描述，然後選取 [下一步]。

    ![在 [基本資訊] 索引標籤上提供自訂角色的名稱和說明](./media/quickstart-app-registration-limits/basics-tab.png)

1. 在 [權限] 索引標籤上的搜尋方塊中，輸入 "microsoft.directory/applications/create"，並選取所需權限旁的核取方塊，然後選取 [下一步]。

    ![在 [權限] 索引標籤上選取自訂角色的權限](./media/quickstart-app-registration-limits/permissions-tab.png)

1. 在 [檢閱 + 建立] 索引標籤上檢閱權限，然後選取 [建立]。

### <a name="assign-the-role-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中指派角色

1. 使用 Azure AD 組織中的特殊權限角色管理員或全域管理員權限登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [Azure Active Directory]，然後選取 [角色和系統管理員]。
1. 選取「應用程式註冊建立者」角色，然後選取 [新增指派]。
1. 選取所需的使用者，然後按一下 [選取]，將使用者新增至角色。

完成！ 在本快速入門中，您已成功建立自訂角色，並使其有權建立不限數目的應用程式註冊，然後將該角色指派給使用者。

> [!TIP]
> 若要使用 Azure AD 入口網站將角色指派給應用程式，請在 [指派] 頁面的搜尋方塊中輸入應用程式的名稱。 應用程式依預設不會顯示在清單中，但會在搜尋結果中傳回。

### <a name="app-registration-permissions"></a>應用程式註冊權限

有兩種權限可用來授與建立應用程式註冊的能力，兩者各有其不同的行為。

- microsoft.directory/applications/createAsOwner：指派此權限會使建立者新增為已建立之應用程式註冊的第一個擁有者，且已建立的應用程式註冊將計入建立者 250 個的物件建立配額中。
- microsoft.directory/applications/create：指派此權限會使建立者不被新增為已建立之應用程式註冊的第一個擁有者，且已建立的應用程式註冊將不會計入建立者 250 個的物件建立配額中。 請謹慎使用此權限，因為在達到目錄層級配額之前，受託人將可不受限地建立應用程式註冊。 如果同時指派這兩種權限，將優先使用此權限。

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>在 Azure AD PowerShell 中建立自訂角色

### <a name="prepare-powershell"></a>準備 PowerShell

首先，從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 然後，使用下列命令匯入 Azure AD PowerShell 預覽模組：

```powershell
import-module azureadpreview
```

若要確認模組已可供使用，請將下列命令所傳回的版本與此處所列的版本進行比對：

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>在 Azure AD PowerShell 中建立自訂角色

使用下列 PowerShell 指令碼建立新的角色：

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>在 Azure AD PowerShell 中指派角色

使用下列 PowerShell 指令碼指派角色：

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>在 Microsoft Graph API 中建立自訂角色

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
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>在 Microsoft Graph API 中指派角色

角色指派結合了安全性主體識別碼 (可以是使用者或服務主體)、角色定義 (角色) 識別碼，以及 Azure AD 資源範圍。

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
- 如需 Azure AD 角色指派的詳細資訊，請參閱[指派管理員角色](permissions-reference.md)。
- 如需預設使用者權限的詳細資訊，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
