---
title: 了解資源鎖定
description: 瞭解 Azure 藍圖中的鎖定選項,以在分配藍圖時保護資源。
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 86897ae6665f7a339b51aaae5f1c00144d8b7309
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437740"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 藍圖中的資源鎖定

如果有一種機制可維護環境一致性，那麼大規模建立一貫化的環境才真正有用。 本文說明 Azure 藍圖中資源鎖定的運作方式。 要查看資源鎖定和應用_拒絕分配_的範例,請參閱[保護新資源](../tutorials/protect-new-resources.md)教程。

> [!NOTE]
> Azure 藍圖部署的資源鎖僅適用於藍圖分配部署的資源。 現有資源(如資源組中已經存在的資源)沒有向它們添加鎖。

## <a name="locking-modes-and-states"></a>鎖定模式和狀態

鎖定模式適用於藍圖分配,它有三個選項:**不鎖定**、**唯讀**或**不刪除**。 鎖定模式會在藍圖指派期間的成品部署過程中進行設定。 您可以藉由更新藍圖指派來設定不同的鎖定模式。
不過，鎖定模式無法變更藍圖外部。

藍圖分配中工件建立的資源有四種狀態:**未鎖定**,**唯**讀,**無法編輯 / 刪除**或**無法刪除**。 每個成品類型都可以處於**未鎖定**狀態。 下表可用來判斷資源的狀態：

|[模式]|成品資源類型|State|描述|
|-|-|-|-|
|不要鎖定|*|未鎖定|資源並未受到藍圖保護。 此狀態也可用於從藍圖指派外部新增至**唯讀**或**不要刪除**資源群組成品的資源。|
|唯讀|資源群組|無法編輯/刪除|此資源群組是唯讀的，而且無法修改資源群組上的標記。 **未鎖定**資源可以在這個資源群組中新增、移動、變更或刪除。|
|唯讀|非資源群組|唯讀|資源不能以任何方式更改：無法變更且無法刪除。|
|不要刪除|*|無法刪除|資源可以改變，但無法刪除。 **未鎖定**資源可以在這個資源群組中新增、移動、變更或刪除。|

## <a name="overriding-locking-states"></a>覆寫鎖定狀態

訂用帳戶中具有適當[角色型存取控制](../../../role-based-access-control/overview.md) (RBAC) 的人 (例如「擁有者」角色) 通常能夠變更或刪除任何資源。 當藍圖將鎖定作為已部署指派的一部分套用時，存取權並非如此。 如果指派是使用 [唯讀]**** 或 [不要刪除]**** 選項來設定的，則即使是訂用帳戶擁有者也無法在受保護的資源上執行封鎖的動作。

此安全性措施可保護已定義藍圖的一致性，以及設計為透過意外或以程式設計方式刪除或修改而建立的環境。

### <a name="assign-at-management-group"></a>在管理群組配置

防止訂閱擁有者刪除藍圖分配的另一個選項是將藍圖分配給管理組。 在這種情況下,只有管理組**的擁有者**具有刪除藍圖分配所需的許可權。

要將藍圖分配給管理組而不是訂閱,REST API 呼叫將更改為如下所示:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

定義的`{assignmentMG}`管理組必須位於管理組層次結構中,或者與保存藍圖定義的管理組相同。

藍圖分配的請求正文如下所示:

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

這個要求正文中的關鍵區別和分配給訂閱的一個屬性是屬性`properties.scope`。 必須將所需的屬性設置為藍圖分配應用於的訂閱。 訂閱必須是存儲藍圖分配的管理組層次結構的直接子級。

> [!NOTE]
> 分配給管理組作用域的藍圖仍作為訂閱級別的藍圖分配運行。 唯一的區別是,藍圖分配的存儲位置是為了防止訂閱所有者刪除分配和相關鎖。

## <a name="removing-locking-states"></a>移除鎖定狀態

如果您需要修改或刪除指派所保護的資源，則可執行此動作的方式有兩種。

- 將藍圖指派更新為**未鎖定**的鎖定模式
- 刪除藍圖指派

移除指派後，則會移除藍圖所建立的鎖定。 不過，資源會留下來，而需要透過正常方式刪除。

## <a name="how-blueprint-locks-work"></a>藍圖鎖定的運作方式

如果指派選取了 [唯讀]**** 或 [不要刪除]**** 選項，則在藍圖指派期間，會將 RBAC [拒絕指派](../../../role-based-access-control/deny-assignments.md)的拒絕動作套用到成品資源。 拒絕動作會由藍圖指派的受控身分識別來新增，並且只能透過相同的受控身分識別從成品資源中移除。 此安全性措施可強制執行鎖定機制，並防止移除藍圖外部的藍圖鎖定。

![藍圖拒絕資源組派](../media/resource-locking/blueprint-deny-assignment.png)

每個模式的[拒絕分配屬性](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)如下所示:

|[模式] |權限.操作 |權限.不操作 |校長[i]。類型 |排除主體[i]。Id | 不套用於兒童範圍 |
|-|-|-|-|-|-|
|唯讀 |**\*** |**\*/讀取** |系統定義 (所有人) |在**排除主體**中,藍圖分配和使用者定義 |資源群組 - _true_;資源 -_錯誤_ |
|不要刪除 |**\*/刪除** | |系統定義 (所有人) |在**排除主體**中,藍圖分配和使用者定義 |資源群組 - _true_;資源 -_錯誤_ |

> [!IMPORTANT]
> Azure Resource Manager 最多可快取 30 分鐘的角色指派詳細資料。 因此，藍圖資源上拒絕指派的拒絕動作可能不會立即完全生效。 在這段時間內，有可能會刪除藍圖鎖定所要保護的資源。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>從拒絕分配中排除主體

在某些設計或安全方案中,可能需要從藍圖分配創建的[拒絕分配](../../../role-based-access-control/deny-assignments.md)中排除主體。 此步驟在 REST API 中完成,在[創建賦值](/rest/api/blueprints/assignments/createorupdate)時,將最多五個值添加到**鎖**屬性中的**排除主體**陣列中。 以下分配定義是包含**排除主體**的請求正文的範例:

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>從拒絕分配中排除操作

與在藍圖分配中[排除](#exclude-a-principal-from-a-deny-assignment)[拒絕分配](../../../role-based-access-control/deny-assignments.md)上的主體類似,可以排除特定的[RBAC 操作](../../../role-based-access-control/resource-provider-operations.md)。 屬性 **.locks**塊中,在**排除主體**的同一位置,可以添加**排除的操作**:

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

雖然**排除主體**必須是顯式的,**但排除的操作**`*`條目可用於 RBAC 操作的通配符匹配。

## <a name="next-steps"></a>後續步驟

- 請按照[保護新資源](../tutorials/protect-new-resources.md)教程進行操作。
- 瞭解[藍圖生命週期](lifecycle.md)。
- 了解如何使用[靜態與動態參數](parameters.md)。
- 了解如何自訂[藍圖排序順序](sequencing-order.md)。
- 瞭解如何[更新現有工作](../how-to/update-existing-assignments.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。