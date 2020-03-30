---
title: 使用 Azure PowerShell - Azure AD 分配自訂角色 |微軟文檔
description: 使用 Azure PowerShell 管理 Azure AD 管理員自訂角色的成員。
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
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025325"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>使用 Azure 活動目錄中的 PowerShell 分配具有資源作用域的自訂角色

本文介紹如何在 Azure 活動目錄 （Azure AD） 中的組織範圍創建角色指派。 在組織範圍中分配角色會授予整個 Azure AD 組織的訪問。 要使用單個 Azure AD 資源的範圍創建角色指派，請參閱[如何創建自訂角色並在資源作用域中分配它](roles-create-custom.md)。本文使用[Azure 活動目錄 PowerShell 版本 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)模組。

有關 Azure AD 管理員角色的詳細資訊，請參閱 [在 Azure 活動目錄中分配管理員角色](directory-assign-admin-roles.md)。

## <a name="required-permissions"></a>所需的權限

使用全域管理員帳戶連接到 Azure AD 租戶以分配或刪除角色。

## <a name="prepare-powershell"></a>準備電源外殼

從[PowerShell 庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 然後使用以下命令導入 Azure AD PowerShell 預覽模組：

``` PowerShell
import-module azureadpreview
```

要驗證模組是否已準備好使用，請將以下命令返回的版本與此處列出的版本匹配：

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

現在您可以開始在模組中使用 Cmdlet。 有關 Azure AD 模組中 Cmdlet 的完整說明，請參閱[Azure AD 預覽模組](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)的線上參考文檔。

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>將角色指派給具有資源範圍的使用者或服務主體

1. 打開 Azure AD 預覽 PowerShell 模組。
1. 通過執行命令`Connect-AzureAD`登錄。
1. 使用以下 PowerShell 腳本創建新角色。

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

要將角色指派給服務主體而不是使用者，請使用[獲取 AzureADMS 服務主體 Cmdlet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)。

## <a name="operations-on-roledefinition"></a>角色定義的操作

角色定義物件包含內置或自訂角色的定義，以及該角色指派授予的許可權。 此資源同時顯示自訂角色定義和內置目錄角色（以角色定義等效形式顯示）。 如今，Azure AD 組織最多可以定義 30 個唯一的自訂角色定義。

### <a name="create-operations-on-roledefinition"></a>創建角色定義操作

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>讀取角色定義操作

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>更新角色定義的操作

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>刪除角色定義上的操作

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>角色指派操作

角色指派包含將給定安全主體（使用者或應用程式服務主體）連結到角色定義的資訊。 如果需要，可以為分配的許可權添加單個 Azure AD 資源的範圍。  對於內置角色和自訂角色，支援限制許可權範圍。

### <a name="create-operations-on-roleassignment"></a>創建角色指派操作

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

### <a name="read-operations-on-roleassignment"></a>讀取角色指派操作

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>刪除角色指派的操作

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>後續步驟

- 在[Azure AD 管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用。
- 有關角色和 Azure AD 管理員角色分配，請參閱[分配管理員角色](directory-assign-admin-roles.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
