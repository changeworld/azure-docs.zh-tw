---
title: 在 Azure AD 以角色為基礎的存取控制中建立自訂角色 |Microsoft Docs
description: 建立並指派 Azure Active Directory 資源上資源範圍的自訂 Azure AD 角色。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74025281"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>在 Azure Active Directory 中建立及指派自訂角色

本文說明如何在 Azure Active Directory （Azure AD）中建立新的自訂角色。 如需自訂角色的基本概念，請參閱[自訂角色總覽](roles-custom-overview.md)。 您可以在目錄層級範圍或僅限應用程式註冊資源範圍指派角色。

您可以在 [Azure AD 總覽] 頁面的 [[角色和系統管理員](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)] 索引標籤中建立自訂角色。

## <a name="create-a-role-in-the-azure-portal"></a>在 Azure 入口網站中建立角色

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>建立新的自訂角色，以授與管理應用程式註冊的存取權

1. 使用特殊權限角色管理員或全域管理員 Azure AD 組織中的許可權登入 [Azure AD 系統管理中心。](https://aad.portal.azure.com)  
1. 選取 [ **Azure Active Directory** > **角色和系統管理員** > ]**新的自訂角色**。

   ![從 [角色和系統管理員] 頁面建立或編輯角色](./media/roles-create-custom/new-custom-role.png)

1. 在 [**基本**] 索引標籤上，提供角色的名稱和描述，然後按 **[下一步]**。

   ![在 [基本] 索引標籤上提供自訂角色的名稱和描述](./media/roles-create-custom/basics-tab.png)

1. 在 [**許可權**] 索引標籤上，選取管理應用程式註冊的基本屬性和認證屬性所需的許可權。 如需每個許可權的詳細說明，請參閱[Azure Active Directory 中的應用程式註冊子類型和許可權](./roles-custom-available-permissions.md)。
   1. 首先，在搜尋列中輸入「認證」，然後選取`microsoft.directory/applications/credentials/update`許可權。

      ![在 [許可權] 索引標籤上選取自訂角色的許可權](./media/roles-create-custom/permissions-tab.png)

   1. 接下來，在搜尋列中輸入「基本」，選取`microsoft.directory/applications/basic/update`許可權，然後按 **[下一步]**。
1. 在 [**審查 + 建立**] 索引標籤上，檢查許可權，然後選取 [**建立**]。

您的自訂角色會顯示在可用角色的清單中，以供指派。

## <a name="create-a-role-using-powershell"></a>使用 PowerShell 建立角色

### <a name="prepare-powershell"></a>準備 PowerShell

首先，您必須[下載 Azure AD Preview PowerShell 模組](https://www.powershellgallery.com/packages/AzureADPreview)。

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

使用下列 PowerShell 腳本建立新的角色：

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 指派自訂角色

使用下列 PowerShell 腳本指派角色：

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

## <a name="create-a-role-with-graph-api"></a>建立具有圖形 API 的角色

1. 建立角色定義。

    建立自訂角色定義的 HTTP 要求。

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

1. 建立角色指派。

    建立自訂角色定義的 HTTP 要求。

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

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>指派範圍為資源的自訂角色

就像內建角色一樣，自訂角色預設會在預設的組織範圍內指派，以授與組織中所有應用程式註冊的存取權限。 但與內建角色不同的是，自訂角色也可以在單一 Azure AD 資源的範圍中指派。 這可讓您授與使用者更新單一應用程式認證和基本屬性的許可權，而不需要建立第二個自訂角色。

1. 使用 Azure AD 組織中應用程式開發人員許可權登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [**應用程式註冊**]。
1. 選取您要授與 [管理] 存取權的應用程式註冊。 您可能必須選取 [**所有應用程式**]，才能在您的 Azure AD 組織中查看應用程式註冊的完整清單。

    ![選取 [應用程式註冊] 做為角色指派的資源範圍](./media/roles-create-custom/appreg-all-apps.png)

1. 在應用程式註冊中，選取 [**角色和系統管理員**]。 如果您尚未建立，請在[上述](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)程式中取得指示。

1. 選取角色以開啟 [**指派**] 頁面。
1. 選取 [**新增指派**] 以新增使用者。 使用者只會被授與所選應用程式註冊的任何許可權。

## <a name="next-steps"></a>後續步驟

- 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
- 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
