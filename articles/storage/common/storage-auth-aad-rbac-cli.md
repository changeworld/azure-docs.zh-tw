---
title: 使用 Azure CLI 為數據訪問分配 RBAC 角色
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI 將許可權分配給具有基於角色的存取控制 （RBAC） 的 Azure 活動目錄安全主體的許可權。 Azure 存儲支援內置和自訂 RBAC 角色，以便通過 Azure AD 進行身份驗證。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74891963"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>使用 Azure CLI 分配 RBAC 角色以訪問 Blob 和佇列資料

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 存儲定義一組內置 RBAC 角色，這些角色包含用於訪問 Blob 或佇列資料的常見許可權集。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全主體可以是 Azure 資源的使用者、組、應用程式服務主體或[託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介紹如何使用 Azure CLI 列出內置 RBAC 角色並將其分配給使用者。 有關使用 Azure CLI 的詳細資訊，請參閱[Azure 命令列介面 （CLI）。](https://docs.microsoft.com/cli/azure)

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>確定資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>列出可用的 RBAC 角色

要使用 Azure CLI 列出可用的內置 RBAC 角色，請使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)命令：

```azurecli-interactive
az role definition list --out table
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

要將 RBAC 角色指派給安全主體，請使用[az 角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)命令。 命令的格式可能因分配範圍而異。 以下示例演示如何在各種作用域中為使用者分配角色，但您可以使用同一命令將角色指派給任何安全主體。

### <a name="container-scope"></a>容器範圍

要將範圍限定為容器的角色，請指定包含`--scope`參數容器範圍的字串。 容器的範圍以以下形式出現：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

下面的示例將**存儲 Blob 資料參與者**角色指派給使用者，該角色範圍限定為容器級別。 請確保將括弧中的樣本值和預留位置值替換為您自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>佇列範圍

要將範圍限定為佇列的角色，請指定包含`--scope`參數佇列範圍的字串。 佇列的範圍以以下形式出現：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

下面的示例將**存儲佇列資料參與者**角色指派給使用者，該使用者的範圍限定為佇列級別。 請確保將括弧中的樣本值和預留位置值替換為您自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>存儲帳戶範圍

要分配限定到存儲帳戶的角色，請指定`--scope`參數的存儲帳戶資源的範圍。 存儲帳戶的範圍以以下形式出現：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

下面的示例演示如何在存儲帳戶級別將**存儲 Blob 資料讀取器**角色指派給使用者。 請確保將示例值替換為您自己的值： |

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>資源組範圍

要分配限定到資源組的角色，請指定`--resource-group`參數的資源組名稱或 ID。 下面的示例將**存儲佇列資料讀取器**角色指派給資源組級別的使用者。 請確保將括弧中的示例值和預留位置值替換為您自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>訂閱範圍

要分配範圍為訂閱的角色，請指定`--scope`參數的訂閱範圍。 訂閱的範圍以以下形式出現：

```
/subscriptions/<subscription>
```

下面的示例演示如何在存儲帳戶級別將**存儲 Blob 資料讀取器**角色指派給使用者。 請確保將示例值替換為您自己的值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>後續步驟

- [使用 RBAC 與 Azure PowerShell 管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-powershell.md)
- [使用 Azure PowerShell 使用 RBAC 授予對 Azure Blob 和佇列資料的訪問](storage-auth-aad-rbac-powershell.md)
- [在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)
