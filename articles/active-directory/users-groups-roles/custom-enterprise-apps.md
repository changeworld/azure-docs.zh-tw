---
title: 企業應用程式存取指派的自訂角色許可權-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 中建立並指派企業應用程式存取的自訂 Azure AD 角色
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 439f31672b5471eb37752e1248704f3f26e44dc1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994874"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>指派自訂角色來管理 Azure Active Directory 中的企業應用程式

本文說明如何建立具有許可權的自訂角色，以在 Azure Active Directory (Azure AD) 中管理使用者和群組的企業應用程式指派。 如需角色指派的元素和術語（例如子類型、許可權和屬性集）的意義，請參閱 [自訂角色總覽](roles-custom-overview.md)。

## <a name="enterprise-app-role-permissions"></a>企業應用程式角色許可權

本文中討論了兩個企業應用程式許可權。 所有範例都使用 update 許可權。

* 若要讀取範圍內的使用者和群組指派，請授與 `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` 許可權
* 若要管理範圍內的使用者和群組指派，請授與 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` 許可權

授與更新許可權會導致「受託人」能夠管理對企業應用程式的使用者和群組指派。 您可以針對單一應用程式授與使用者和/或群組指派的範圍，或為所有應用程式授與。 如果授與整個組織的層級，則受託人可以管理所有應用程式的指派。 如果是在應用層級進行，則受託人只能管理指定之應用程式的指派。

授與 update 許可權的步驟有兩個：

1. 建立具有許可權的自訂角色 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. 授與使用者或群組許可權，以管理企業應用程式的使用者和群組指派。 這是您可以將範圍設定為整個組織的層級，或設定為單一應用程式。

## <a name="use-the-azure-ad-admin-center"></a>使用 Azure AD 系統管理中心

### <a name="create-a-new-custom-role"></a>建立新的自訂角色

>[!NOTE]
> 自訂角色是在整個組織的層級建立和管理，而且只能從組織的 [總覽] 頁面使用。

1. 使用您組織中的特殊許可權角色管理員或全域系統管理員許可權，登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 [Azure Active Directory]****，選取 [角色和系統管理員]****，然後選取 [新增自訂角色]****。

    ![從 Azure AD 中的 [角色] 清單新增自訂角色](./media/custom-enterprise-apps/new-custom-role.png)

1. 在 [ **基本** ] 索引標籤上，提供角色名稱的 [管理使用者和群組指派] 和角色描述的 [授與管理使用者和群組指派的許可權]，然後選取 **[下一步**]。

    ![提供自訂角色的名稱和描述](./media/custom-enterprise-apps/role-name-and-description.png)

1. 在 [ **許可權** ] 索引標籤的 [搜尋] 方塊中輸入 "ServicePrincipals/serviceprincipals.approleassignedto/update"，然後選取所需許可權旁的核取方塊，然後選取 [ **下一步**]。

    ![將許可權新增至自訂角色](./media/custom-enterprise-apps/role-custom-permissions.png)

1. 在 [檢閱 + 建立]**** 索引標籤上檢閱權限，然後選取 [建立]****。

    ![現在您可以建立自訂角色](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>使用 Azure AD 入口網站將角色指派給使用者

1. 使用特殊許可權角色管理員角色許可權登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 [Azure Active Directory]****，然後選取 [角色和系統管理員]****。
1. 選取 [ **授與許可權來管理使用者和群組指派** 角色]。

    ![開啟角色和系統管理員，並搜尋自訂角色](./media/custom-enterprise-apps/select-custom-role.png)

1. 選取 [ **新增指派**]，選取所需的使用者，然後按一下 [ **選取** ] 將角色指派新增至使用者。

    ![將自訂角色的指派新增至使用者](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>指派秘訣

* 若要授與許可權給工作人員，以管理整個企業應用程式的使用者和群組存取權，請從您組織的 Azure AD**總覽**頁面上的全組織**角色和系統管理員**清單開始。
* 若要授與許可權給受託人以管理特定企業應用程式的使用者和群組存取權，請在 Azure AD 中移至該應用程式，然後在該應用程式的 [ **角色和系統管理員** ] 清單中開啟。 選取新的自訂角色，並完成使用者或群組指派。 受託人只能管理特定應用程式的使用者和群組存取權。
* 若要測試您的自訂角色指派，請以「受託人」登入，並開啟應用程式的 [ **使用者和群組** ] 頁面，確認已啟用 [ **新增使用者** ] 選項。

    ![驗證使用者的許可權](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>使用 Azure AD PowerShell

如需詳細資訊，請參閱使用 PowerShell [建立和指派自訂角色](roles-create-custom.md) ，以及 [使用資源範圍指派自訂角色](roles-assign-powershell.md)。

首先，請從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 然後，使用下列命令匯入 Azure AD PowerShell 預覽模組：

```powershell
PowerShell
import-module azureadpreview
```

若要確認模組已可供使用，請將下列命令所傳回的版本與此處所列的版本進行比對：

```powershell
PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>建立自訂角色

使用下列 PowerShell 指令碼建立新的角色：

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>指派自訂角色

使用這個 PowerShell 腳本來指派角色。

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

使用 Microsoft Graph API 中提供的範例來建立自訂角色。 如需詳細資訊，請參閱使用 Microsoft Graph API [建立和指派自訂角色](roles-create-custom.md) ，以及 [指派自訂系統管理員角色](roles-assign-graph.md)。

建立自訂角色的 HTTP 要求。

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
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

角色指派結合了安全性主體識別碼 (可以是使用者或服務主體) 、角色定義識別碼和 Azure AD 資源範圍。 如需有關角色指派元素的詳細資訊，請參閱 [自訂角色總覽](roles-custom-overview.md)

指派自訂角色的 HTTP 要求。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>下一步

* [探索適用于企業應用程式的可用自訂角色許可權](custom-enterprise-app-permissions.md)
