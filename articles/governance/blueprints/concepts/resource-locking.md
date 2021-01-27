---
title: 了解資源鎖定
description: 瞭解 Azure 藍圖中的鎖定選項，以在指派藍圖時保護資源。
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920185"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 藍圖中的資源鎖定

如果有一種機制可維護環境一致性，那麼大規模建立一貫化的環境才真正有用。 本文說明 Azure 藍圖中資源鎖定的運作方式。 若要查看資源鎖定和 _拒絕指派_ 的應用程式範例，請參閱 [保護新資源](../tutorials/protect-new-resources.md) 教學課程。

> [!NOTE]
> Azure 藍圖部署的資源鎖定只會套用至藍圖指派所部署的資源。 現有的資源（例如資源群組中已存在的資源）不會加入鎖定。

## <a name="locking-modes-and-states"></a>鎖定模式和狀態

鎖定模式適用于藍圖指派，它有三個選項： [ **不要鎖定**]、[ **唯讀**] 或 [不要 **刪除**]。 鎖定模式會在藍圖指派期間的成品部署過程中進行設定。 您可以藉由更新藍圖指派來設定不同的鎖定模式。
但是，鎖定模式無法在 Azure 藍圖之外變更。

藍圖指派中成品所建立的資源有四種狀態： [ **未鎖定**]、[ **唯讀**]、[ **無法編輯/刪除**] 或 [ **無法刪除**]。 每個成品類型都可以處於 **未鎖定** 狀態。 下表可用來判斷資源的狀態：

|模式|成品資源類型|州|描述|
|-|-|-|-|
|不要鎖定|*|未鎖定|資源不受 Azure 藍圖保護。 此狀態也可用於從藍圖指派外部新增至 **唯讀** 或 **不要刪除** 資源群組成品的資源。|
|唯讀|資源群組|無法編輯/刪除|此資源群組是唯讀的，而且無法修改資源群組上的標記。 **未鎖定** 資源可以在這個資源群組中新增、移動、變更或刪除。|
|唯讀|非資源群組|唯讀|資源無法以任何方式改變。 沒有變更，也無法刪除。|
|不要刪除|*|無法刪除|資源可以改變，但無法刪除。 **未鎖定** 資源可以在這個資源群組中新增、移動、變更或刪除。|

## <a name="overriding-locking-states"></a>覆寫鎖定狀態

具有適當 [azure 角色型存取控制 ](../../../role-based-access-control/overview.md) 的人員通常可能會在訂用帳戶上 (azure RBAC) ，例如「擁有者」角色，才能修改或刪除任何資源。 當 Azure 藍圖套用鎖定作為已部署指派的一部分時，就不會發生此存取。 如果指派是使用 [唯讀] 或 [不要刪除] 選項來設定的，則即使是訂用帳戶擁有者也無法在受保護的資源上執行封鎖的動作。

此安全性措施可保護已定義藍圖的一致性，以及設計為透過意外或以程式設計方式刪除或修改而建立的環境。

### <a name="assign-at-management-group"></a>指派于管理群組

防止訂用帳戶擁有者移除藍圖指派的唯一選項，就是將藍圖指派給管理群組。 在此案例中， **只有管理群組的擁有者** 具有移除藍圖指派所需的許可權。

為了將藍圖指派給管理群組，而不是訂用帳戶，REST API 呼叫的變更如下所示：

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

由定義的管理群組 `{assignmentMG}` 必須位於管理群組階層內，或必須是儲存藍圖定義所在的相同管理群組。

藍圖指派的要求主體看起來像這樣：

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

此要求主體和指派給訂用帳戶的主要差異在於 `properties.scope` 屬性。 此必要屬性必須設定為藍圖指派套用的訂用帳戶。 訂用帳戶必須是儲存藍圖指派之管理群組階層的直接子系。

> [!NOTE]
> 指派給管理群組範圍的藍圖仍會以訂用帳戶層級藍圖指派的形式運作。 唯一的差異在於儲存藍圖指派的位置，可防止訂用帳戶擁有者移除指派和相關聯的鎖定。

## <a name="removing-locking-states"></a>移除鎖定狀態

如果您需要修改或刪除指派所保護的資源，則可執行此動作的方式有兩種。

- 將藍圖指派更新為 **未鎖定** 的鎖定模式
- 刪除藍圖指派

移除指派之後，會移除 Azure 藍圖所建立的鎖定。 不過，資源會留下來，而需要透過正常方式刪除。

## <a name="how-blueprint-locks-work"></a>藍圖鎖定的運作方式

如果指派選取 [**唯讀**] 或 [**不要刪除**] 選項，則在指派藍圖期間，會將 Azure RBAC [拒絕指派](../../../role-based-access-control/deny-assignments.md)拒絕動作套用至成品資源。 拒絕動作會由藍圖指派的受控身分識別來新增，並且只能透過相同的受控身分識別從成品資源中移除。 此安全性措施會強制執行鎖定機制，並防止移除 Azure 藍圖外部的藍圖鎖定。

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="資源群組的 [存取控制 (我的 M) ] 頁面和 [拒絕指派] 索引標籤的螢幕擷取畫面。" border="false":::

每個模式的 [拒絕指派屬性](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) 如下所示：

|模式 |許可權。動作 |許可權。 NotActions |主體 [i]。類型 |ExcludePrincipals [i]。Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|唯讀 |**\** _ |_ *\* /read **<br />** microsoft. 授權/鎖定/刪除 **<br />** Microsoft. Network/virtualNetwork/subnet/join/action** |SystemDefined (每個人)  |**excludedPrincipals** 中的藍圖指派和使用者定義 |資源群組- _true_;資源- _false_ |
|不要刪除 |**\*/delete** | **Microsoft.Authorization/locks/delete**<br />**Microsoft. Network/virtualNetwork/subnet/join/action** |SystemDefined (每個人)  |**excludedPrincipals** 中的藍圖指派和使用者定義 |資源群組- _true_;資源- _false_ |

> [!IMPORTANT]
> Azure Resource Manager 最多可快取 30 分鐘的角色指派詳細資料。 因此，藍圖資源上拒絕指派的拒絕動作可能不會立即完全生效。 在這段時間內，有可能會刪除藍圖鎖定所要保護的資源。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>從拒絕指派中排除主體

在某些設計或安全性案例中，可能需要從藍圖指派建立的 [拒絕指派](../../../role-based-access-control/deny-assignments.md) 中排除主體。 此步驟會在 REST API 中完成，方法是在 [建立指派](/rest/api/blueprints/assignments/createorupdate)時，將最多五個值新增至 [**鎖定**] 屬性中的 **excludedPrincipals** 陣列。 下列指派定義是包含 **excludedPrincipals** 的要求主體範例：

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>從拒絕指派中排除動作

類似于在藍圖指派中排除[拒絕指派](../../../role-based-access-control/deny-assignments.md)上[的主體](#exclude-a-principal-from-a-deny-assignment)，您可以排除特定的[Azure 資源提供者](../../../role-based-access-control/resource-provider-operations.md)作業。 在 **excludedPrincipals** **為的相同** 位置中，您可以新增 **excludedActions** ：

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

雖然 **excludedPrincipals** 必須是明確的，但 **excludedActions** 專案可利用 `*` 來與資源提供者作業的萬用字元相符。

## <a name="next-steps"></a>後續步驟

- 遵循 [ [保護新資源](../tutorials/protect-new-resources.md) ] 教學課程。
- 了解[藍圖生命週期](./lifecycle.md)。
- 了解如何使用[靜態與動態參數](./parameters.md)。
- 了解如何自訂[藍圖排序順序](./sequencing-order.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。
