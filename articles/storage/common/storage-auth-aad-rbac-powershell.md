---
title: 使用 PowerShell 為數據訪問分配 RBAC 角色
titleSuffix: Azure Storage
description: 瞭解如何使用 PowerShell 將許可權分配給具有基於角色的存取控制 （RBAC） 的 Azure 活動目錄安全主體的許可權。 Azure 存儲支援內置和自訂 RBAC 角色，以便通過 Azure AD 進行身份驗證。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460578"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>使用 PowerShell 分配 RBAC 角色以訪問 Blob 和佇列資料

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體會定義一組內建的 RBAC 角色，其中包含一般用來存取容器或佇列的權限集合。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全主體可以是 Azure 資源的使用者、組、應用程式服務主體或[託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介紹如何使用 Azure PowerShell 列出內置 RBAC 角色並將其分配給使用者。 有關使用 Azure PowerShell 的詳細資訊，請參閱[Azure PowerShell 的概述](https://docs.microsoft.com/powershell/azure/overview)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>確定資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>列出可用的 RBAC 角色

要使用 Azure PowerShell 列出可用的內置 RBAC 角色，請使用[獲取 ARole 定義](/powershell/module/az.resources/get-azroledefinition)命令：

```powershell
Get-AzRoleDefinition | FT Name, Description
```

您將看到列出的內置 Azure 存儲資料角色以及 Azure 的其他內置角色：

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>將 RBAC 角色指派給安全主體

要將 RBAC 角色指派給安全主體，請使用[New-AzRole 分配](/powershell/module/az.resources/new-azroleassignment)命令。 命令的格式可能因分配範圍而異。 為了運行該命令，您需要在相應的作用域中分配擁有者或參與者角色。 以下示例演示如何在各種作用域中為使用者分配角色，但您可以使用同一命令將角色指派給任何安全主體。

### <a name="container-scope"></a>容器範圍

要將範圍限定為容器的角色，請指定包含`--scope`參數容器範圍的字串。 容器的範圍以以下形式出現：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

下面的示例將**存儲 Blob 資料參與者**角色指派給使用者，該使用者範圍限定為名為*示例容器的容器*。 請確保將括弧中的樣本值和預留位置值替換為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>佇列範圍

要將範圍限定為佇列的角色，請指定包含`--scope`參數佇列範圍的字串。 佇列的範圍以以下形式出現：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

下面的示例將**存儲佇列資料參與者**角色指派給使用者，該使用者限定為名為*示例佇列的佇列*。 請確保將括弧中的樣本值和預留位置值替換為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>存儲帳戶範圍

要分配限定到存儲帳戶的角色，請指定`--scope`參數的存儲帳戶資源的範圍。 存儲帳戶的範圍以以下形式出現：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

下面的示例演示如何在存儲帳戶級別將**存儲 Blob 資料讀取器**角色範圍限定給使用者。 請確保將示例值替換為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>資源組範圍

要分配限定到資源組的角色，請指定`--resource-group`參數的資源組名稱或 ID。 下面的示例將**存儲佇列資料讀取器**角色指派給資源組級別的使用者。 請確保將括弧中的示例值和預留位置值替換為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>訂閱範圍

要分配範圍為訂閱的角色，請指定`--scope`參數的訂閱範圍。 訂閱的範圍以以下形式出現：

```
/subscriptions/<subscription>
```

下面的示例演示如何在存儲帳戶級別將**存儲 Blob 資料讀取器**角色指派給使用者。 請確保將示例值替換為您自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>後續步驟

- [使用 RBAC 與 Azure PowerShell 管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-powershell.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)
