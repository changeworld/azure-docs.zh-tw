---
title: 在 Azure AD 基於角色的存取控制中創建自訂角色 |微軟文檔
description: 在 Azure 活動目錄資源上創建和分配具有資源作用域的自訂 Azure AD 角色。
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025281"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>在 Azure 活動目錄中創建和分配自訂角色

本文介紹如何在 Azure 活動目錄 （Azure AD） 中創建新的自訂角色。 有關自訂角色的基礎知識，請參閱[自訂角色概述](roles-custom-overview.md)。 角色只能在目錄級作用域或應用註冊資源作用域分配。

可以在 Azure AD 概述頁上[的角色和管理員](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)選項卡中創建自訂角色。

## <a name="create-a-role-in-the-azure-portal"></a>在 Azure 門戶中創建角色

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>創建新的自訂角色以授予管理應用註冊的許可權

1. 在 Azure [AD](https://aad.portal.azure.com) 組織中使用特權角色管理員或全域管理員許可權登錄到 Azure AD 管理中心。
1. 選擇**Azure 活動目錄** > **角色和管理員** > **"新建自訂角色**"。

   ![從角色和管理員頁面創建或編輯角色](./media/roles-create-custom/new-custom-role.png)

1. 在 **"基本"** 選項卡上，為角色提供名稱和說明，然後按一下"**下一步**"。

   ![在"基本"選項卡上提供自訂角色的名稱和說明](./media/roles-create-custom/basics-tab.png)

1. 在 **"許可權"** 選項卡上，選擇管理應用註冊的基本屬性和憑據屬性所需的許可權。 有關每個許可權的詳細說明，請參閱[Azure 活動目錄中的應用程式註冊子類型和許可權](./roles-custom-available-permissions.md)。
   1. 首先，在搜索欄中輸入"憑據"並`microsoft.directory/applications/credentials/update`選擇許可權。

      ![在"許可權"選項卡上選擇自訂角色的許可權](./media/roles-create-custom/permissions-tab.png)

   1. 接下來，在搜索欄中輸入"基本"，選擇`microsoft.directory/applications/basic/update`許可權，然後按一下"**下一步**"。
1. 在 **"審閱 + 創建**"選項卡上，查看許可權並選擇"**創建**"。

自訂角色將顯示在要分配的現有角色清單中。

## <a name="create-a-role-using-powershell"></a>使用 PowerShell 創建角色

### <a name="prepare-powershell"></a>準備電源外殼

首先，必須[下載 Azure AD 預覽電源外殼模組](https://www.powershellgallery.com/packages/AzureADPreview)。

若要安裝 AzureAD PowerShell 模組，請使用下列命令︰

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

若要確認此模組已可使用，請使用下列命令︰

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>建立自訂角色

使用以下 PowerShell 腳本創建新角色：

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 分配自訂角色

使用以下 PowerShell 腳本分配角色：

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>使用圖形 API 創建角色

1. 創建角色定義。

    HTTP 要求以創建自訂角色定義。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    body

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. 創建角色指派。

    HTTP 要求以創建自訂角色定義。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>為資源配置限定的自訂角色

與內置角色一樣，預設情況下，自訂角色在預設組織範圍範圍內分配，以授予組織中所有應用註冊的存取權限。 但與內置角色不同，自訂角色也可以在單個 Azure AD 資源的範圍內分配。 這允許您授予使用者更新單個應用的憑據和基本屬性的許可權，而無需創建第二個自訂角色。

1. 在 Azure [AD](https://aad.portal.azure.com)組織中使用應用程式開發人員許可權登錄到 Azure AD 管理中心。
1. 選擇**應用註冊**。
1. 選擇要授予管理存取權限的應用註冊。 您可能需要選擇 **"所有應用程式"** 才能查看 Azure AD 組織中應用註冊的完整清單。

    ![選擇應用註冊作為角色指派的資源範圍](./media/roles-create-custom/appreg-all-apps.png)

1. 在應用註冊中，選擇**角色和管理員**。 如果尚未創建一個，則說明位於[前面的過程中](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)。

1. 選擇要打開 **"分配"** 頁的角色。
1. 選擇 **"添加分配**"以添加使用者。 使用者將被授予任何許可權，僅通過選定的應用註冊。

## <a name="next-steps"></a>後續步驟

- 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
- 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
