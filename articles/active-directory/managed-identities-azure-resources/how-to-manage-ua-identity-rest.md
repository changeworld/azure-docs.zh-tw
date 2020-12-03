---
title: 使用 REST Azure AD 管理使用者指派的受控識別
description: 如何建立、列出和刪除使用者指派的受控識別，以進行 REST API 呼叫的逐步指示。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546687"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>使用 REST API 呼叫來建立、列出或刪除使用者指派的受控識別

適用于 Azure 資源的受控識別可讓 Azure 服務向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您會了解如何使用 CURL 進行 REST API 呼叫，來建立、列出和刪除使用者指派的識別。

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱 [系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 您可以在雲端或本機上執行本文中的所有命令：
    - 若要在雲端中執行，請使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。
    - 若要在本機執行，請安裝 [捲曲](https://curl.haxx.se/download.html) 和 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="obtain-a-bearer-access-token"></a>取得持有人存取權杖

1. 如果在本機執行，請透過 Azure CLI 登入 Azure：

    ```
    az login
    ```

1. 使用[az 帳戶取得](/cli/azure/account#az_account_get_access_token)存取權杖取得存取權杖

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別 

若要建立使用者指派的受控識別，您的帳戶需要[受控識別參與者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色指派。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**要求標頭**

|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        |

**要求本文**

|Name  |說明  |
|---------|---------|
|location     | 必要。 資源位置。        |

## <a name="list-user-assigned-managed-identities"></a>列出使用者指派的受控識別

若要列出/讀取使用者指派的受控識別，您的帳戶需要[受控識別操作者](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[受控識別參與者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色指派。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        |

## <a name="delete-a-user-assigned-managed-identity"></a>刪除使用者指派的受控識別

若要刪除使用者指派的受控識別，您的帳戶需要[受控識別參與者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色指派。

> [!NOTE]
> 將使用者指派的受控識別從受指派的任何資源中刪除，並不會移除參考。 若要使用 CURL 從虛擬機器移除使用者指派的受控識別，請參閱[從 Azure VM 移除使用者指派的識別](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm)。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        |

## <a name="next-steps"></a>後續步驟

如需如何使用 CURL 將使用者指派的受控識別指派給 Azure VM/VMSS 的資訊，請參閱[使用 REST API 呼叫在 Azure VM 上設定 Azure 資源的受控識別](qs-configure-rest-vm.md#user-assigned-managed-identity) (英文) 和[使用 REST API 呼叫在虛擬機器擴展集上設定 Azure 資源的受控識別](qs-configure-rest-vmss.md#user-assigned-managed-identity) \(英文\)。
