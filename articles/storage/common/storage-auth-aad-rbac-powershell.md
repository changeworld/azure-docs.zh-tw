---
title: 使用 PowerShell 指派 Azure 角色以進行資料存取
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure PowerShell 模組，利用 azure 角色型存取控制，將許可權指派給 Azure Active Directory 安全性主體 (Azure RBAC) 。 Azure 儲存體支援內建和 Azure 自訂角色，以透過 Azure AD 進行驗證。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19262441df87b96bbb43a010ca47861ec2b236d3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488753"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>使用 PowerShell 指派 Azure 角色以存取 blob 和佇列資料

Azure Active Directory (Azure AD) 透過 [AZURE RBAC (的 azure 角色型存取控制 ](../../role-based-access-control/overview.md)，來授與受保護資源的存取權限。 Azure 儲存體會定義一組 Azure 內建角色，其中包含用來存取容器或佇列的一般許可權集。

當 Azure 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 的安全性主體可能是使用者、群組、應用程式服務主體，或 [適用于 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

本文說明如何使用 Azure PowerShell 來列出 Azure 內建角色，並將其指派給使用者。 如需使用 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 的總覽](/powershell/azure/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>適用于 blob 和佇列的 Azure 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>判斷資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>列出可用的 Azure 角色

若要使用 Azure PowerShell 列出可用的 Azure 內建角色，請使用 [>get-azroledefinition](/powershell/module/az.resources/get-azroledefinition) 命令：

```powershell
Get-AzRoleDefinition | FT Name, Description
```

您會看到列出內建的 Azure 儲存體資料角色，以及其他適用于 Azure 的內建角色：

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>將 Azure 角色指派給安全性主體

若要將 Azure 角色指派給安全性主體，請使用 [>new-azroleassignment](/powershell/module/az.resources/new-azroleassignment) 命令。 命令的格式會根據指派的範圍而有所不同。 若要執行命令，您必須在對應的範圍中指派「擁有者」或「參與者」角色。 下列範例示範如何將角色指派給不同範圍的使用者，但您可以使用相同的命令將角色指派給任何安全性主體。

### <a name="container-scope"></a>容器範圍

若要指派範圍為容器的角色，請指定包含參數容器範圍的字串 `--scope` 。 容器的範圍格式如下：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

下列範例會將 **儲存體 Blob 資料參與者** 角色指派給使用者，範圍設定為 *範例容器*的容器。 請務必以您自己的值取代括弧中的範例值和預留位置值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>佇列範圍

若要指派範圍為佇列的角色，請指定包含參數佇列範圍的字串 `--scope` 。 佇列的範圍格式如下：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

下列範例會將 **儲存體佇列資料參與者** 角色指派給使用者，範圍設定為名為 *範例佇列*的佇列。 請務必以您自己的值取代括弧中的範例值和預留位置值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>儲存體帳戶範圍

若要指派範圍限定于儲存體帳戶的角色，請指定參數的儲存體帳戶資源範圍 `--scope` 。 儲存體帳戶的範圍格式如下：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

下列範例示範如何將 **儲存體 Blob 資料讀取器** 角色的範圍限定在儲存體帳戶層級的使用者。 請務必將範例值取代為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>資源群組範圍

若要指派範圍設定為資源群組的角色，請指定參數的資源組名或識別碼 `--resource-group` 。 下列範例會將「 **儲存體佇列資料讀者** 」角色指派給資源群組層級的使用者。 請務必以您自己的值取代括弧中的範例值和預留位置值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>訂用帳戶範圍

若要指派訂用帳戶範圍內的角色，請為參數指定訂用帳戶的範圍 `--scope` 。 訂用帳戶的範圍格式如下：

```
/subscriptions/<subscription>
```

下列範例顯示如何將 **儲存體 Blob 資料讀取器** 角色指派給儲存體帳戶層級的使用者。 請務必將範例值取代為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>後續步驟

- [使用 Azure PowerShell 模組新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-powershell.md)
- [使用 Azure CLI 指派 Azure 角色以存取 blob 和佇列資料](storage-auth-aad-rbac-cli.md)
- [使用 Azure 入口網站指派 Azure 角色以存取 blob 和佇列資料](storage-auth-aad-rbac-portal.md)
