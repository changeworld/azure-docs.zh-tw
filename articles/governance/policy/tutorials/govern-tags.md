---
title: 教學課程：管理標籤治理
description: 在本教學課程中，使用 Azure 原則的 Modify 效果，在新的和現有的資源上建立和強制執行標籤治理模型。
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 5e9cb9a4acb930c117374281a3debaeecce47110
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75965989"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>教學課程：使用 Azure 原則管理標籤治理

[標籤](../../../azure-resource-manager/management/tag-resources.md)是將 Azure 資源組織成分類的重要部分。 當您依照[標籤管理的最佳做法](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)操作時，標籤可作為使用 Azure 原則來套用商務原則或[使用成本管理來追蹤成本](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources)的基礎。
無論您使用標籤的方式或原因為何，能夠在 Azure 資源上快速新增、變更和移除這些標籤，都是十分重要的。

Azure 原則的 [Modify](../concepts/effects.md#modify) 效果可用來協助您管理標籤，而不論您處於哪個資源治理階段。 **Modify** 可在下列情況下發揮作用：

- 您不熟悉雲端，且沒有任何標籤治理
- 已有數以千計的資源，但沒有標籤治理
- 已有需要變更的現有分類法

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> - 識別您的商務需求
> - 將每項需求對應至原則定義
> - 將標籤原則分組成一個計畫

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="identify-requirements"></a>識別需求

就像任何良好的治理控制實作一樣，這些需求應來自於您的商務需求，並且在建立技術控制之前受到充分了解。 在此案例教學課程中，我們的商務需求如下：

- 所有資源都有兩個必要標籤：_CostCenter_ 和 _Env_
- 所有容器和個別資源上都必須有 _CostCenter_
  - 資源繼承自其所在的容器，但可能會個別遭到覆寫
- 所有容器和個別資源上都必須有 _Env_
  - 資源會依據容器命名配置來決定環境，且不會遭到覆寫
  - 容器中的所有資源都是相同環境的一部分

## <a name="configure-the-costcenter-tag"></a>設定 CostCenter 標籤

就 Azure 原則所管理的 Azure 環境而言，_CostCenter_ 標籤需求如下：

- 拒絕遺漏 _CostCenter_ 標籤的資源群組
- 在遺漏 _CostCenter_ 標籤時，修改資源以從父資源群組新增該標籤

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>拒絕遺漏 CostCenter 標籤的資源群組

由於資源群組的 _CostCenter_ 無法由資源群組的名稱決定，因此必須在建立資源群組的要求上定義標籤。 具有 [Deny](../concepts/effects.md#deny) 效果的下列原則規則，會防止建立或更新沒有 _CostCenter_ 標籤的資源群組：

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> 當此原則規則以資源群組為目標時，原則定義上的_模式_必須是「全部」，而不是「已編製索引」。

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>在遺漏 CostCenter 標記時，修改資源以繼承該標籤

第二個 _CostCenter_ 需求是要讓任何資源在標籤遺漏時從父資源群組繼承標籤。 如果已在資源上定義該標籤，即使該標籤與父資源群組不同，也必須保持原狀。 下列原則規則會使用 [Modify](../concepts/effects.md#modify)：

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

此原則規則會使用 **add** 作業，而不是 **addOrReplace**，因為我們不想要在[修復](../how-to/remediate-resources.md)現有資源時修改現有的標籤值。 它也會使用 `[resourcegroup()]` 範本函式來取得父資源群組中的標籤值。

> [!NOTE]
> 當此原則規則以支援標籤的資源為目標時，原則定義上的_模式_必須是「已編製索引」。 這種設定也可確保此原則會略過資源群組。

## <a name="configure-the-env-tag"></a>設定 Env 標籤

就 Azure 原則所管理的 Azure 環境而言，_Env_ 標籤需求如下：

- 根據資源群組的命名配置，修改資源群組的 _Env_ 標籤
- 將資源群組中所有資源的 _Env_ 標籤修改為與父資源群組相同的標籤

### <a name="modify-resource-groups-env-tag-based-on-name"></a>根據名稱修改資源群組 Env 標籤

存在於 Azure 環境中的每個環境都需要 [Modify](../concepts/effects.md#modify) 原則。 每個環境的 Modify 原則會類似於如下的原則定義：

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> 當此原則規則以資源群組為目標時，原則定義上的_模式_必須是「全部」，而不是「已編製索引」。

符合此原則的資源群組，僅限於將範例命名配置用於 `prd-` 之生產資源的群組。 若要達到更複雜的命名配置，必須使用數個**比對**條件，而不是此範例中的單一 **like**。

### <a name="modify-resources-to-inherit-the-env-tag"></a>修改資源以繼承 Env 標籤

依據商務需求，所有資源都必須具有其父資源群組所具備的 _Env_ 標籤。 此標籤無法覆寫，因此我們將使用搭配 [Modify](../concepts/effects.md#modify) 效果的 **addOrReplace** 作業。 範例 Modify 原則會如下列規則所示：

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> 當此原則規則以支援標籤的資源為目標時，原則定義上的_模式_必須是「已編製索引」。 這種設定也可確保此原則會略過資源群組。

此原則規則會尋找未以其父資源群組值作為 _Env_ 標籤，或遺漏了 _Env_ 標籤的任何資源。 比對資源會將其 _Env_ 標籤設定為父資源群組值，即使該標籤已存在於資源上，但具有不同的值，仍是如此。

## <a name="assign-the-initiative-and-remediate-resources"></a>指派方案並補救資源

在建立上述標籤原則後，請將其加入標籤治理的單一方案中，並將其指派給管理群組或訂用帳戶。 方案和其中包含的原則接著會評估現有資源的合規性，並針對與原則規則中的 **if** 屬性相符的新資源或更新資源變更要求。 不過，原則並不會自動以定義的標籤變更來更新不符合規範的現有資源。

如同 [deployIfNotExists](../concepts/effects.md#deployifnotexists) 原則，**Modify** 原則也會使用補救工作來改變不符合規範的現有資源。 依照[如何補救資源](../how-to/remediate-resources.md)的指示識別不符合規範的 **Modify** 資源，並將標籤更正為您已定義的分類。

## <a name="clean-up-resources"></a>清除資源

如果您已完成使用本教學課程中的資源，請使用下列步驟來刪除前面建立的任何指派或定義：

1. 選取 Azure 原則頁面左側 [製作]  下的 [定義]  (如果您嘗試刪除指派，則選取 [指派]  )。

1. 搜尋您要移除的新計畫或原則定義 (或指派)。

1. 以滑鼠右鍵按一下資料列，或選取定義 (或指派) 結尾的省略符號，然後選取 [刪除定義]  (或 [刪除指派]  )。

## <a name="review"></a>檢閱

在本教學課程中，您已了解下列下列工作：

> [!div class="checklist"]
> - 識別您的商務需求
> - 將每項需求對應至原則定義
> - 將標籤原則分組成一個方案

## <a name="next-steps"></a>後續步驟

若要深入了解原則定義的結構，請閱讀這篇文章：

> [!div class="nextstepaction"]
> [Azure 原則定義結構](../concepts/definition-structure.md)