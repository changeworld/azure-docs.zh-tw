---
title: 了解部署順序
description: 瞭解在藍圖指派期間部署藍圖構件的預設順序，以及如何自訂部署順序。
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 8305e5d44caef0f35e5b4beb4b70be9736272fa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051469"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 藍圖中的部署順序

Azure 藍圖會使用 **排序次序** 來決定處理藍圖定義指派時的資源建立順序。 本文說明下列概念：

- 使用的預設排序順序
- 如何自訂順序
- 如何處理自訂的順序

在 JSON 範例中有一些變數。您需要使用自己的值取代它們：

- `{YourMG}` - 以您的管理群組名稱取代

## <a name="default-sequencing-order"></a>預設排序順序

如果藍圖定義未針對部署成品的順序包含任何指示詞，或指示詞為 null，則會使用下列順序：

- 訂用帳戶層級**角色指派**成品，依成品名稱排序
- 訂用帳戶層級**原則指派**成品，依成品名稱排序
- 訂用帳戶層級 **Azure Resource Manager 範本** (ARM 範本) 以成品名稱排序的構件
- **資源群組**成品 (包含子成品)，依預留位置名稱排序

在每個**資源群組**成品中，下列排序順序會適用於要在該資源群組內建立的成品：

- 資源群組子**角色指派**成品，依成品名稱排序
- 資源群組子**原則指派**成品，依成品名稱排序
- 資源群組子 **Azure Resource Manager 範本** (ARM 範本) 以成品名稱排序的構件

> [!NOTE]
> 使用成品 [ ( # B1 ](../reference/blueprint-functions.md#artifacts) 會對所參考的構件建立隱含相依性。

## <a name="customizing-the-sequencing-order"></a>自訂排序順序

撰寫大型藍圖定義時，可能需要以特定順序建立資源。 此案例最常見的使用模式是藍圖定義包含數個 ARM 範本。 Azure 藍圖藉由允許定義排序次序來處理此模式。

排序可透過在 JSON 中定義 `dependsOn` 屬性來完成。 資源群組和成品物件的藍圖定義支援此屬性。 `dependsOn` 為成品名稱字串陣列，代表在其建立前必須建立的特定成品。

> [!NOTE]
> 建立藍圖物件時，如果使用 [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)或 URL 端點，則每個成品資源都會從檔案名取得其名稱（如果使用 [REST API](/rest/api/blueprints/artifacts/createorupdate)）。 成品中的_resourceGroup_參考必須符合藍圖定義中定義的專案。

### <a name="example---ordered-resource-group"></a>範例-已排序的資源群組

此藍圖定義範例有一個資源群組，它會宣告的值以及 `dependsOn` 標準資源群組，以定義自訂排序次序。 在此範例中，名為 **assignPolicyTags** 的成品會在 **ordered-rg** 資源群組之前進行處理。
**standard-rg** 會根據預設排序順序進行處理。

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>範例 - 使用自訂順序的成品

此範例是相依于 ARM 範本的原則成品。 依預設順序，會在 ARM 範本之前建立原則成品。 此順序可讓原則成品等候 ARM 範本建立。

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>範例-依據資源群組的訂用帳戶層級範本成品

此範例適用于在訂用帳戶層級部署的 ARM 範本，以相依于資源群組。 在預設順序中，訂用帳戶層級成品會在這些資源群組中的任何資源群組和子構件之前建立。 資源群組是在藍圖定義中定義，如下所示：

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

訂用帳戶層級範本成品的定義方式 **如下所示** ：

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>處理自訂順序

在建立過程期間，會使用拓撲排序來建立藍圖成品的相依性關係圖。 此檢查可確保支援資源群組與成品之間的每個相依性層級。

如果成品相依性宣告為不會改變預設順序，則不會進行任何變更。
有個範例是相依於訂用帳戶層級原則的資源群組。 另一個範例則是相依於資源群組 ’standard-rg’ 子角色指派的資源群組 ’standard-rg’ 子原則指派。 在這兩個案例中，`dependsOn` 皆不會改變預設排序順序，因此不會產生任何變更。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](./lifecycle.md)。
- 了解如何使用[靜態與動態參數](./parameters.md)。
- 了解如何使用[藍圖資源鎖定](./resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。