---
title: 在 Azure AD 角色型存取控制中建立自訂角色 | Microsoft Docs
description: 建立並指派具有 Azure Active Directory 資源上資源範圍的自訂 Azure AD 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0adb88cd49dd3de8649703ed45821c68a31776e
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914462"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>在 Azure Active Directory 中建立及指派自訂角色

本文說明如何在 Azure Active Directory (Azure AD) 中建立新的自訂角色。 如需自訂角色的基本概念，請參閱[自訂角色概觀](custom-overview.md)。 您可以在目錄層級範圍或僅限應用程式註冊資源範圍指派角色。

您可以在 Azure AD 概觀頁面的 [[角色和系統管理員](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)] 索引標籤中建立自訂角色。

## <a name="create-a-role-in-the-azure-portal"></a>在 Azure 入口網站中建立角色

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>建立新的自訂角色以授與可管理應用程式註冊的存取權

1. 使用 Azure AD 組織的特殊權限角色管理員或全域系統管理員許可權登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 [Azure Active Directory] > [角色和系統管理員] > [新增自訂角色]。

   ![從 [角色和系統管理員] 頁面建立或編輯角色](./media/custom-create/new-custom-role.png)

1. 在 [基本資訊] 索引標籤上提供角色的名稱和說明，然後按 [下一步]。

   ![在 [基本資訊] 索引標籤上提供自訂角色的名稱和說明](./media/custom-create/basics-tab.png)

1. 在 [權限] 索引標籤上，選取要管理應用程式註冊的基本屬性和認證屬性所需的權限。 如需每個權限的詳細說明，請參閱 [Azure Active Directory 中的應用程式註冊子類型和權限](custom-available-permissions.md)。
   1. 首先，在搜尋列中輸入「認證」，然後選取 [`microsoft.directory/applications/credentials/update`] 權限。

      ![在 [權限] 索引標籤上選取自訂角色的權限](./media/custom-create/permissions-tab.png)

   1. 接下來，在搜尋列中輸入「基本」、選取 [`microsoft.directory/applications/basic/update`] 權限，然後按 [下一步]。
1. 在 [檢閱 + 建立] 索引標籤上檢閱權限，然後選取 [建立]。

您的自訂角色就會顯示在可供指派的角色清單中。

## <a name="create-a-role-using-powershell"></a>使用 PowerShell 建立角色

### <a name="prepare-powershell"></a>準備 PowerShell

首先，您必須[下載 Azure AD 預覽 PowerShell 模組](https://www.powershellgallery.com/packages/AzureADPreview)。

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
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>連線到 Azure

若要連接到 Azure Active Directory，請使用下列命令：

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>建立自訂角色

使用下列 PowerShell 指令碼建立新的角色：

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

使用下列 PowerShell 指令碼指派角色：

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

## <a name="create-a-role-with-graph-api"></a>使用圖形 API 建立角色

1. 建立角色定義。

    用來建立自訂角色定義的 HTTP 要求。

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

    > [!Note]
    > `"templateId": "GUID"`是根據需求在主體中傳送的選擇性參數。 如果您需要使用一般參數建立多個不同的自訂角色，最好是建立範本並定義一個 `templateId` 值。 您可以 `templateId` 事先使用 PowerShell Cmdlet 來產生值 `(New-Guid).Guid` 。 

1. 建立角色指派。

    用來建立自訂角色定義的 HTTP 要求。

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

和內建角色一樣，自訂角色預設也會指派到預設的整個組織範圍，以授與組織中所有應用程式註冊的存取權限。 但與內建角色不同的是，自訂角色也可以指派到單一 Azure AD 資源的範圍。 這可讓您向使用者提供可更新單一應用程式認證和基本屬性的權限，而不需要建立第二個自訂角色。

1. 使用 Azure AD 組織中應用程式開發人員的許可權登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 **應用程式註冊**。
1. 選取所要授與的管理存取權適用的應用程式註冊。 您可能必須選取 [所有應用程式] 才能查看 Azure AD 組織中的完整應用程式註冊清單。

    ![選取 [應用程式註冊] 作為角色指派的資源範圍](./media/custom-create/appreg-all-apps.png)

1. 在 [應用程式註冊] 中選取 [角色和系統管理員]。 如果您尚未建立，相關指示位於[上一個程序](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)中。

1. 選取角色以開啟 [指派] 頁面。
1. 選取 [新增指派] 以新增使用者。 使用者只會獲得所選應用程式註冊的任何權限。

## <a name="next-steps"></a>後續步驟

- 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
- 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](permissions-reference.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context)。