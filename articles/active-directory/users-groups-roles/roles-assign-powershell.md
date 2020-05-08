---
title: 使用 Azure PowerShell Azure AD 來指派自訂角色 |Microsoft Docs
description: 使用 Azure PowerShell 管理 Azure AD 系統管理員自訂角色的成員。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfcb00f56a3cc6f71729fbeaf317ce9447e120ed
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582601"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>在 Azure Active Directory 中使用 PowerShell 指派具有資源範圍的自訂角色

本文說明如何在 Azure Active Directory （Azure AD）中的組織範圍內建立角色指派。 在整個組織範圍指派角色，會授與 Azure AD 組織的存取權。 若要建立具有單一 Azure AD 資源範圍的角色指派，請參閱[如何建立自訂角色，並在資源範圍中指派](roles-create-custom.md)。本文使用[Azure Active Directory PowerShell 第2版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)模組。

如需 Azure AD 管理員角色的詳細資訊，請參閱 [在 Azure Active Directory 中指派系統管理員角色](directory-assign-admin-roles.md)。

## <a name="required-permissions"></a>所需的權限

使用全域管理員帳戶連接到您的 Azure AD 組織，以指派或移除角色。

## <a name="prepare-powershell"></a>準備 PowerShell

從[PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 然後使用下列命令匯入 Azure AD PowerShell 預覽模組：

``` PowerShell
import-module azureadpreview
```

若要確認模組已備妥可供使用，請將下列命令所傳回的版本與此處所列的版本進行比對：

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

現在您可以開始在模組中使用 Cmdlet。 如需 Azure AD 模組中 Cmdlet 的完整描述，請參閱[Azure AD preview 模組](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)的線上參考檔。

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>將角色指派給具有資源範圍的使用者或服務主體

1. 開啟 Azure AD preview PowerShell 模組。
1. 執行命令`Connect-AzureAD`以登入。
1. 使用下列 PowerShell 腳本建立新的角色。

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

若要將角色指派給服務主體，而不是使用者，請使用[AzureADMSServicePrincipal Cmdlet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)。

## <a name="operations-on-roledefinition"></a>RoleDefinition 上的作業

角色定義物件包含內建或自訂角色的定義，以及該角色指派所授與的許可權。 此資源會同時顯示自訂角色定義和內建 directoryRoles （以 roleDefinition 對應的形式顯示）。 目前，Azure AD 的組織最多隻能定義30個唯一的自訂 RoleDefinitions。

### <a name="create-operations-on-roledefinition"></a>在 RoleDefinition 上建立作業

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

### <a name="read-operations-on-roledefinition"></a>RoleDefinition 上的讀取作業

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>RoleDefinition 上的更新作業

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>RoleDefinition 上的刪除作業

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>RoleAssignment 上的作業

角色指派包含將指定的安全性主體（使用者或應用程式服務主體）連結至角色定義的資訊。 如有需要，您可以為指派的許可權新增單一 Azure AD 資源的範圍。  內建和自訂角色支援限制許可權的範圍。

### <a name="create-operations-on-roleassignment"></a>在 RoleAssignment 上建立作業

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

### <a name="read-operations-on-roleassignment"></a>RoleAssignment 上的讀取作業

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>RoleAssignment 上的刪除作業

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>後續步驟

- 請在[Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)與我們分享。
- 如需角色和 azure AD 系統管理員角色指派的詳細資訊，請參閱[指派系統管理員角色](directory-assign-admin-roles.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
