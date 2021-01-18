---
title: 使用 Azure AD PowerShell Azure AD 指派自訂角色 |Microsoft Docs
description: 使用 Azure AD PowerShell 管理 Azure AD 系統管理員自訂角色的成員。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b155ccd7f8f0d7f6d63d906d7d0baaa3243512b
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562772"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>在 Azure Active Directory 中使用 PowerShell 以資源範圍指派自訂角色

本文說明如何在 Azure Active Directory (Azure AD) 的全組織範圍內建立角色指派。 在整個組織範圍指派角色，會授與跨 Azure AD 組織的存取權。 若要建立具有單一 Azure AD 資源範圍的角色指派，請參閱 [如何建立自訂角色，並在資源範圍中指派該角色](custom-create.md)。 本文使用 [Azure Active Directory PowerShell 第2版](/powershell/module/azuread/#directory_roles) 模組。

如需 Azure AD 系統管理員角色的詳細資訊，請參閱 [Azure Active Directory 中指派系統管理員角色](permissions-reference.md)。

## <a name="required-permissions"></a>所需的權限

使用全域管理員帳戶連接到您的 Azure AD 組織，以指派或移除角色。

## <a name="prepare-powershell"></a>準備 PowerShell

從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview)安裝 Azure AD PowerShell 模組。 然後，使用下列命令匯入 Azure AD PowerShell 預覽模組：

``` PowerShell
Import-Module AzureADPreview
```

若要確認模組已可供使用，請將下列命令所傳回的版本與此處所列的版本進行比對：

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

現在您可以開始在模組中使用 Cmdlet。 如需 Azure AD 模組中 Cmdlet 的完整描述，請參閱 [Azure AD preview 模組](https://www.powershellgallery.com/packages/AzureADPreview)的線上參考檔。

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>使用資源範圍將目錄角色指派給使用者或服務主體

1. 載入 Azure AD PowerShell (Preview) 模組。
1. 執行命令以登入 `Connect-AzureAD` 。
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

若要將角色指派給服務主體，而不是使用者，請使用 [AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) Cmdlet。

## <a name="role-definitions"></a>角色定義

角色定義物件包含內建或自訂角色的定義，以及該角色指派所授與的許可權。 此資源會顯示自訂角色定義和內建目錄角色 (以 roleDefinition 等同的表單) 來顯示。 現今 Azure AD 的組織最多可以定義30個唯一的自訂角色定義。

### <a name="create-a-role-definition"></a>建立角色定義

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>讀取和列出角色定義

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>更新角色定義

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>刪除角色定義

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>角色指派

角色指派包含連結指定安全性主體 (使用者或應用程式服務主體) 至角色定義的資訊。 如有需要，您可以為指派的許可權新增單一 Azure AD 資源的範圍。  內建和自訂角色都支援限制角色指派的範圍。

### <a name="create-a-role-assignment"></a>建立角色指派

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

### <a name="read-and-list-role-assignments"></a>讀取和列出角色指派

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>刪除角色指派

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>後續步驟

- 在[Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)中與我們分享
- 如需角色和 Azure AD 系統管理員角色指派的詳細資訊，請參閱[指派系統管理員角色](permissions-reference.md)。
- 如需預設的使用者權限，請參閱[預設來賓和成員使用者許可權的比較](../fundamentals/users-default-permissions.md)。
