---
title: 瞭解 Azure RBAC 的範圍
description: 瞭解 Azure 角色型存取控制的範圍 (Azure RBAC) 以及如何判斷資源的範圍。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: ad906e3665c6ffc354cf6292c2559d1184037594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856140"
---
# <a name="understand-scope-for-azure-rbac"></a>瞭解 Azure RBAC 的範圍

*範圍* 是一組存取適用的資源。 當您指派角色時，請務必瞭解範圍，讓您可以只授與安全性主體真正需要的存取權。 藉由限制範圍，如果安全性主體遭盜用，您可以限制哪些資源有風險。

## <a name="scope-levels"></a>範圍層級

在 Azure 中，您可以在四個層級指定範圍： [管理群組](../governance/management-groups/overview.md)、訂用帳戶、 [資源群組](../azure-resource-manager/management/overview.md#resource-groups)和資源。 範圍的結構為父子式關聯性。 每個階層層級可讓範圍更加具體。 您可以在任何範圍層級指派角色。 您選取的層級會決定角色的套用幅度。 較低層級會從較高的層級繼承角色許可權。 

![角色指派的範圍](./media/scope-overview/rbac-scope-no-label.png)

管理群組是訂用帳戶之上的範圍層級，但管理群組支援更複雜的階層。 下圖顯示您可以定義之管理群組和訂用帳戶的階層範例。 如需管理群組的詳細資訊，請參閱 [什麼是 Azure 管理群組？](../governance/management-groups/overview.md)。

![管理群組和訂用帳戶階層](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>範圍格式

如果您使用命令列新增角色指派，您必須指定範圍。 針對命令列工具，範圍是可能長的字串，可識別角色指派的確切範圍。 在 Azure 入口網站中，此範圍通常會列為 *資源識別碼*。

範圍是由一連串以斜線 (/) 字元分隔的識別碼所組成。 您可以將此字串視為下列階層的表示法，其中，不含預留位置的文字 (`{}`) 是固定識別碼：

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` 是要使用之訂用帳戶的識別碼 (GUID)。
- `{resourcesGroupName}` 是包含資源群組的名稱。
- `{providerName}` 是處理資源的 [資源提供者](../azure-resource-manager/management/azure-services-resource-providers.md) 名稱，然後 `{resourceType}` `{resourceSubType*}` 在該資源提供者內找出進一步的層級。
- `{resourceName}` 是識別特定資源之字串的最後一個部分。

管理群組是訂用帳戶之上的層級，且最廣泛的 (特定) 範圍。 此層級的角色指派適用于管理群組內的訂用帳戶。 管理群組的範圍具有下列格式：

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>範圍範例

> [!div class="mx-tableFixed"]
> | 影響範圍 | 範例 |
> | --- | --- |
> | 管理群組 | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | 訂用帳戶 | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | 資源群組 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | 資源 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>如何判斷資源的範圍

判斷管理群組、訂用帳戶或資源群組的範圍相當簡單。 您只需要知道名稱和訂用帳戶識別碼。 不過，判斷資源的範圍需要更多工作。 您可以透過幾種方式來判斷資源的範圍。

- 在 Azure 入口網站中，開啟資源，然後查看屬性。 資源應該會列出您可以在其中判斷範圍的 **資源識別碼** 。 例如，以下是儲存體帳戶的資源識別碼。

    ![Azure 入口網站中儲存體帳戶的資源識別碼](./media/scope-overview/scope-resource-id.png)

- 另一種方式是使用 Azure 入口網站在資源範圍暫時指派角色，然後使用 [Azure PowerShell](role-assignments-list-powershell.md) 或 [Azure CLI](role-assignments-list-cli.md) 來列出角色指派。 在輸出中，範圍會列為屬性。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>後續步驟

- [新增角色指派的步驟](role-assignments-steps.md)
- [Azure 服務的資源提供者](../azure-resource-manager/management/azure-services-resource-providers.md) \(部分機器翻譯\)
- [什麼是 Azure 管理群組？](../governance/management-groups/overview.md)
