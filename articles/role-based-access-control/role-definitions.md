---
title: 了解適用於 Azure 資源之 RBAC 中的角色定義 | Microsoft Docs
description: 了解角色型存取控制 (RBAC) 中的角色定義，以對 Azure 資源的存取管理進行微調。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 777ea7cc29679a3819e94d39913f167ea1cb3453
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641387"
---
# <a name="understand-role-definitions-for-azure-resources"></a>了解適用於 Azure 資源的角色定義

如果您想要嘗試了解角色的運作方式，或是想要自行建立[適用於 Azure 資源的自訂角色](custom-roles.md)，了解角色的定義方式將會很有幫助。 本文詳細描述角色定義並提供一些範例。

## <a name="role-definition"></a>角色定義

「角色定義」  是權限集合。 有時簡稱為「角色」**。 角色定義會列出可執行的作業，例如讀取、寫入和刪除。 也可能列出無法執行的作業或與基礎資料相關的作業。 角色定義具有下列屬性：

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

| 屬性 | 描述 |
| --- | --- |
| `Name` | 角色的顯示名稱。 |
| `Id` | 角色的唯一識別碼。 |
| `IsCustom` | 表示這是否為自訂角色。 若為自訂角色，請設定為 `true`。 |
| `Description` | 角色的描述。 |
| `Actions` | 字串陣列，指定角色允許執行的管理作業。 |
| `NotActions` | 字串陣列，指定從所允許 `Actions` 中排除的管理作業。 |
| `DataActions` | 字串陣列，指定角色允許對物件內資料執行的管理作業。 |
| `NotDataActions` | 字串陣列，指定從所允許 `DataActions` 中排除的資料作業。 |
| `AssignableScopes` | 字串陣列，指定角色可用於指派的範圍。 |

### <a name="operations-format"></a>作業格式

用來指定作業的字串具有下列格式：

- `{Company}.{ProviderName}/{resourceType}/{action}`

作業字串的 `{action}` 部分指定您可以對資源類型執行的作業類型。 例如，您將會在 `{action}` 中看到下列子字串：

| 動作子字串    | 描述         |
| ------------------- | ------------------- |
| `*` | 此萬用字元會授與所有符合字串之作業的存取權。 |
| `read` | 啟用讀取作業 (GET)。 |
| `write` | 啟用寫入作業（PUT 或 PATCH）。 |
| `action` | 啟用像是重新開機虛擬機器（POST）之類的自訂作業。 |
| `delete` | 啟用刪除作業 (DELETE)。 |

### <a name="role-definition-example"></a>角色定義範例

以下是 JSON 格式的[參與者](built-in-roles.md#contributor)角色定義。 `Actions` 下的萬用字元 (`*`) 作業表示指派給這個角色的主體可以執行所有動作；換句話說，它可以管理所有項目。 這包括未來 Azure 新增資源類型時所定義的動作。 `NotActions` 下的作業會從 `Actions` 扣除。 如果是[參與者](built-in-roles.md#contributor)角色，`NotActions` 會移除此角色管理資源存取權及指派資源存取權的功能。

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>管理和資料作業

管理作業的角色型存取控制是在角色定義的 `Actions` 和 `NotActions` 屬性中指定。 以下是 Azure 中的一些管理作業範例：

- 管理儲存體帳戶的存取權
- 建立、更新或刪除 Blob 容器
- 刪除資源群組及其所有資源

如果容器驗證方法設定為「Azure AD 使用者帳戶」而非「存取金鑰」，則不會將管理存取權繼承至您的資料。 此隔離可防止具有萬用字元 (`*`) 的角色不受限地存取您的資料。 例如，如果使用者具有訂用帳戶的[讀取者](built-in-roles.md#reader)角色，則可以檢視儲存體帳戶，但預設為無法檢視基礎資料。

在此之前，資料作業不可使用角色型存取控制。 資料作業的授權會因為資源提供者不同而有差異。 用於管理作業的相同角色型存取控制授權模型已擴充至資料作業。

為了支援資料作業，新的資料屬性已新增至角色定義。 資料作業會在 `DataActions` 和 `NotDataActions` 屬性中指定。 藉由新增這些資料屬性，可繼續維持管理和資料之間的分隔。 這可避免目前具有萬用字元 (`*`) 的角色指派突然存取資料。 以下是可在 `DataActions` 和 `NotDataActions`中指定的一些資料作業：

- 讀取容器中的 Blob 清單
- 將儲存體 Blob 寫入容器中
- 刪除佇列中的訊息

以下是[儲存體 Blob 資料讀取器](built-in-roles.md#storage-blob-data-reader)角色定義，其中包含`Actions`和`DataActions`屬性中的作業。 此角色可讓您讀取 Blob 容器和基礎 Blob 資料。

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

您只能將資料作業新增至 `DataActions` 和 `NotDataActions` 屬性。 資源提供者會藉由將 `isDataAction` 屬性設為 `true`，來識別哪些作業是資料作業。 若要查看 `isDataAction` 為 `true` 的作業清單，請參閱[資源提供者作業](resource-provider-operations.md)。 在角色定義中，沒有資料作業的角色不需要有 `DataActions` 和 `NotDataActions` 屬性。

所有管理作業 API 呼叫的授權都會由 Azure Resource Manager 處理。 資料作業 API 呼叫的授權是由資源提供者或 Azure Resource Manager 處理。

### <a name="data-operations-example"></a>資料作業範例

若要進一步了解管理和資料作業如何運作，我們來看特定範例。 Alice 已在訂用帳戶範圍上獲得[擁有者](built-in-roles.md#owner)角色的指派。 Bob 已獲指派儲存體帳戶範圍的[儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色。 此範例如下圖所示。

![角色型存取控制已延伸為可支援管理和資料作業](./media/role-definitions/rbac-management-data.png)

Alice 的「[擁有](built-in-roles.md#owner)者」角色和 Bob 的「[儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)」角色具有下列動作：

擁有者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

儲存體 Blob 資料參與者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

由於 Alice 在訂用帳戶`*`範圍有萬用字元（）動作，因此其許可權會向下繼承，讓他們能夠執行所有管理動作。 Alice 可以讀取、寫入和刪除容器。 不過，Alice 無法在未採取額外步驟的情況下執行資料作業。 例如，根據預設，Alice 無法讀取容器內的 Blob。 若要讀取 Blob，Alice 必須擷取儲存體存取金鑰，並使用它們來存取 Blob。

Bob 的許可權僅限於`Actions` [儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色中所指定的和`DataActions` 。 以此角色為基礎，Bob 可以執行管理和資料作業。 例如，Bob 可以在指定的儲存體帳戶中讀取、寫入和刪除容器，也可以讀取、寫入和刪除 blob。

如需適用於儲存體之管理及資料平面安全性的詳細資訊，請參閱 [Azure 儲存體安全性指南](../storage/blobs/security-recommendations.md)。

### <a name="what-tools-support-using-rbac-for-data-operations"></a>哪些工具支援對資料作業使用 RBAC？

若要檢視及使用資料作業，您必須有正確版本的工具或 SDK：

| 工具  | 版本  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 或更新版本 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 或更新版本 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0-預覽或更新版本 |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 或更新版本 |
| [Azure for Java](/java/azure/) | 1.9.0 或更新版本 |
| [Azure for Python](/azure/python/) | 0.40.0 或更新版本 |
| [適用於 Ruby 的 Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 或更新版本 |

若要在 REST API 中檢視及使用資料作業，您必須將 **api-version** 參數設為下列版本或更新版本：

- 2018-07-01

## <a name="actions"></a>動作

`Actions` 權限會指定角色所允許執行的管理作業。 它是識別 Azure 資源提供者的安全性實體作業的作業字串集合。 以下是可用於 `Actions` 中的一些管理作業範例。

> [!div class="mx-tableFixed"]
> | 作業字串    | 描述         |
> | ------------------- | ------------------- |
> | `*/read` | 授與所有 Azure 資源提供者的所有資源類型之讀取作業的存取權。|
> | `Microsoft.Compute/*` | 授與對 Microsoft.Compute 資源提供者中所有資源類型之所有作業的存取權。|
> | `Microsoft.Network/*/read` | 授與 Microsoft.Network 資源提供者的所有資源類型之讀取作業的存取權。|
> | `Microsoft.Compute/virtualMachines/*` | 授與虛擬機器及其子資源類型之所有作業的存取權。|
> | `microsoft.web/sites/restart/Action` | 授與重新啟動 Web 應用程式的存取權。|

## <a name="notactions"></a>NotActions

`NotActions` 權限指定從所允許 `Actions` 中排除的管理作業。 如果排除限制的作業可更輕鬆地定義您要允許的作業集合，請使用 `NotActions` 權限。 角色 (有效權限) 所授與之存取權的計算方式是將 `Actions` 作業扣除 `NotActions` 作業。

> [!NOTE]
> 如果為使用者指派會排除 `NotActions` 中作業的角色，並指派授與相同作業存取權的第二個角色，即會允許使用者執行該作業。 `NotActions` 不是拒絕規則 - 它只是一個便利的方式，可以在需要排除特定作業時建立允許的作業集合。
>

## <a name="dataactions"></a>DataActions

`DataActions` 權限會指定角色允許對物件內資料執行的管理作業。 例如，如果使用者有儲存體帳戶的讀取 Blob 資料存取權，則他們可讀取該儲存體帳戶中的 Blob。 以下是可用於 `DataActions` 中的一些資料作業範例。

> [!div class="mx-tableFixed"]
> | 作業字串    | 描述         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | 傳回 Blob 或 Blob 清單。 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | 傳回寫入 Blob 的結果。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | 傳回訊息。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | 傳回訊息或寫入或刪除訊息的結果。 |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions` 權限可指定從所允許 `DataActions` 中排除的資料作業。 角色 (有效權限) 所授與之存取權的計算方式是將 `DataActions` 作業扣除 `NotDataActions` 作業。 每個資源提供者會提供個別的一組 API 來完成資料作業。

> [!NOTE]
> 如果為使用者指派的角色已在 `NotDataActions` 中排除一個資料作業，並且指派授與相同資料作業存取權的第二個角色給使用者，即會允許使用者執行該資料作業。 `NotDataActions` 不是拒絕規則 - 它只是一個便利的方式，可以在需要排除特定資料作業時建立允許的資料作業集合。
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes`屬性會指定具有此角色定義的範圍（管理群組、訂用帳戶或資源群組）。 您可以只將角色指派給需要它的管理群組、訂用帳戶或資源群組。 您必須至少使用一個管理群組、訂用帳戶或資源群組。

內建角色的 `AssignableScopes` 設定為根目錄範圍 (`"/"`)。 根目錄範圍表示角色可指派給所有範圍。 有效的可指派範圍範例包括：

> [!div class="mx-tableFixed"]
> | 角色可供指派 | 範例 |
> |----------|---------|
> | 一個訂用帳戶 | `"/subscriptions/{subscriptionId1}"` |
> | 兩個訂閱 | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | 網路資源群組 | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | 一個管理群組 | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | 管理群組和訂用帳戶 | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | 所有範圍（僅適用于內建角色） | `"/"` |

如需適用於自訂角色之 `AssignableScopes` 的相關資訊，請參閱[適用於 Azure 資源的自訂角色](custom-roles.md)。

## <a name="next-steps"></a>後續步驟

* [適用於 Azure 資源的內建角色](built-in-roles.md)
* [適用於 Azure 資源的自訂角色](custom-roles.md)
* [Azure Resource Manager 資源提供者作業](resource-provider-operations.md)
