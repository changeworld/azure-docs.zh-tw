---
title: 了解部署順序
description: 瞭解藍圖專案在藍圖分配期間部署的預設順序以及如何自定義部署順序。
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677570"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 藍圖中的部署順序

Azure 藍圖在處理藍圖定義的分配時使用**排序順序**來確定資源創建的順序。 本文說明下列概念：

- 使用的預設排序順序
- 如何自訂順序
- 如何處理自訂的順序

在 JSON 範例中有一些變數。您需要使用自己的值取代它們：

- `{YourMG}` - 以您的管理群組名稱取代

## <a name="default-sequencing-order"></a>預設排序順序

如果藍圖定義不包含用於部署工件的順序的指令,或者該指令為空,則使用以下順序:

- 訂用帳戶層級**角色指派**成品，依成品名稱排序
- 訂用帳戶層級**原則指派**成品，依成品名稱排序
- 訂用帳戶層級 **Azure Resource Manager 範本**成品，依成品名稱排序
- **資源群組**成品 (包含子成品)，依預留位置名稱排序

在每個**資源群組**成品中，下列排序順序會適用於要在該資源群組內建立的成品：

- 資源群組子**角色指派**成品，依成品名稱排序
- 資源群組子**原則指派**成品，依成品名稱排序
- 資源群組子 **Azure Resource Manager 範本**成品，依成品名稱排序

> [!NOTE]
> 使用[工件()](../reference/blueprint-functions.md#artifacts)會創建對所引用工件的隱式依賴項。

## <a name="customizing-the-sequencing-order"></a>自訂排序順序

在編寫大型藍圖定義時,可能需要按特定順序創建資源。 此方案的最常見使用模式是藍圖定義包含多個 Azure 資源管理器範本時。 Azure 藍圖通過允許定義排序順序來處理此模式。

排序可透過在 JSON 中定義 `dependsOn` 屬性來完成。 資源組和工件物件的藍圖定義支援此屬性。 `dependsOn` 為成品名稱字串陣列，代表在其建立前必須建立的特定成品。

> [!NOTE]
> 建立藍圖物件時,如果使用[PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)或 URL 終結點(如果使用[REST API),](/rest/api/blueprints/artifacts/createorupdate)則每個專案資源從檔名獲取其名稱。
> 專案中的_資源組_引用必須與藍圖定義中定義的引用匹配。

### <a name="example---ordered-resource-group"></a>範例 - 資源群組

此示例藍圖定義具有一個資源組,該資源組通過聲明 的`dependsOn`值 以及標準資源組來定義自定義排序順序。 在此範例中，名為 **assignPolicyTags** 的成品會在 **ordered-rg** 資源群組之前進行處理。
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

此範例是相依於 Azure Resource Manager 範本的原則成品。 根據預設順序，原則成品會在 Azure Resource Manager 範本之前建立。 這順序可讓原則成品等待建立 Azure Resource Manager 範本。

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

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>範例 - 訂閱等級範本項目,具體取決於資源群組

此範例適用於部署在訂閱等級的資源管理器範本,以依賴於資源組。 在預設排序中,將在這些資源組中的任何資源組和子專案之前創建訂閱級專案。 資源組在藍圖定義中定義,如下所示:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

訂閱等級樣本項目(取決於**等待-me)** 資源組的定義如下所示:

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

如果成品相依性宣告為不會改變預設順序，則不會進行任何變更。 有個範例是相依於訂用帳戶層級原則的資源群組。 另一個範例則是相依於資源群組 ’standard-rg’ 子角色指派的資源群組 ’standard-rg’ 子原則指派。 在這兩個案例中，`dependsOn` 皆不會改變預設排序順序，因此不會產生任何變更。

## <a name="next-steps"></a>後續步驟

- 瞭解[藍圖生命週期](lifecycle.md)。
- 了解如何使用[靜態與動態參數](parameters.md)。
- 了解如何使用[藍圖資源鎖定](resource-locking.md)。
- 瞭解如何[更新現有工作](../how-to/update-existing-assignments.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。