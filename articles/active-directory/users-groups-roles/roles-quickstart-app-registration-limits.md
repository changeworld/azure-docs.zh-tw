---
title: 刪除創建應用註冊的限制 - Azure AD |微軟文檔
description: 分配自訂角色以在 Azure AD 活動目錄中授予不受限制的應用註冊
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559040"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>快速入門：授予創建無限制應用註冊的許可權

在此快速入門中，您將創建一個自訂角色，具有創建無限數量的應用註冊的許可權，然後將該角色指派給使用者。 然後，分配的使用者可以使用 Azure AD 門戶、Azure AD PowerShell 或 Microsoft 圖形 API 創建應用程式註冊。 與內置應用程式開發人員角色不同，此自訂角色授予創建無限數量的應用程式註冊的能力。 應用程式開發人員角色授予該功能，但創建的物件總數限制為 250，以防止達到[目錄範圍的物件配額](directory-service-limits-restrictions.md)。

如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

## <a name="prerequisite"></a>必要條件

創建和分配 Azure AD 自訂角色所需的最低特權角色是特權角色管理員。

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>使用 Azure AD 門戶創建新的自訂角色

1. 在 Azure [AD](https://aad.portal.azure.com) 組織中使用特權角色管理員或全域管理員許可權登錄到 Azure AD 管理中心。
1. 選擇**Azure 活動目錄**，選擇**角色和管理員**，然後選擇 **"新建自訂角色**"。

    ![從角色和管理員頁面創建或編輯角色](./media/roles-create-custom/new-custom-role.png)

1. 在 **"基本"** 選項卡上，為角色的名稱提供"應用程式註冊建立者"，並為角色描述提供"可以創建無限數量的應用程式註冊"，然後選擇 **"下一步**"。

    ![在"基本"選項卡上提供自訂角色的名稱和說明](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. 在 **"許可權"** 選項卡上，在搜索框中輸入"microsoft.directory/應用程式/創建"，然後選擇所需許可權旁邊的核取方塊，然後選擇 **"下一步**"。

    ![在"許可權"選項卡上選擇自訂角色的許可權](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. 在 **"審閱 + 創建**"選項卡上，查看許可權並選擇"**創建**"。

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>使用 Azure AD 門戶將角色指派給使用者

1. 在 Azure [AD](https://aad.portal.azure.com) 組織中使用特權角色管理員或全域管理員許可權登錄到 Azure AD 管理中心。
1. 選擇**Azure 活動目錄**，然後選擇**角色和管理員**。
1. 選擇應用程式註冊建立者角色並選擇 **"添加分配**"。
1. 選擇所需的使用者，然後按一下 **"選擇"** 將使用者添加到角色。

完成！ 在此快速入門中，您成功創建了一個自訂角色，並有權創建無限數量的應用註冊，然後將該角色指派給使用者。

> [!TIP]
> 要使用 Azure AD 門戶將角色指派給應用程式，請在分配頁的搜索框中輸入應用程式的名稱。 預設情況下，應用程式不顯示在清單中，但在搜尋結果中返回。

### <a name="app-registration-permissions"></a>應用註冊許可權

有兩個許可權可用於授予創建應用程式註冊的功能，每個許可權具有不同的行為。

- Microsoft.directory/應用程式/createAsowner：分配此許可權會導致建立者添加為創建的應用註冊的第一個擁有者，並且創建的應用註冊將計入建立者的 250 個創建物件配額。
- Microsoft.directory/應用程式策略/創建：分配此許可權會導致建立者未添加為已創建應用註冊的第一個擁有者，並且創建的應用註冊不會計入建立者的 250 個創建物件配額。 請謹慎使用此許可權，因為在命中目錄級配額之前，不會阻止受讓人創建應用註冊。 如果同時分配了兩個許可權，則此許可權優先。

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 創建自訂角色

使用以下 PowerShell 腳本創建新角色：

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 分配自訂角色

#### <a name="prepare-powershell"></a>準備電源外殼

首先，從[PowerShell 庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 然後使用以下命令導入 Azure AD PowerShell 預覽模組：

```powershell
import-module azureadpreview
```

要驗證模組是否已準備好使用，請將以下命令返回的版本與此處列出的版本匹配：

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>分配自訂角色

使用以下 PowerShell 腳本分配角色：

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>使用 Microsoft 圖形 API 創建自訂角色

HTTP 要求以創建自訂角色。

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>使用 Microsoft 圖形 API 分配自訂角色

角色指派結合了安全主體 ID（可以是使用者或服務主體）、角色定義（角色）ID 和 Azure AD 資源作用域。

分配自訂角色的 HTTP 要求。

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
