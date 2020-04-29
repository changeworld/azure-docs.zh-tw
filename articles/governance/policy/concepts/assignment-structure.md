---
title: 原則指派結構的詳細資料
description: 描述 Azure 原則用來將原則定義和參數關聯至資源進行評估的原則指派定義。
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: cdb2fc0c6f057ece44383f68bc79fca54507db9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683209"
---
# <a name="azure-policy-assignment-structure"></a>Azure 原則指派結構

Azure 原則會使用原則指派來定義哪些資源已指派哪些原則或計畫。 原則指派可以在指派時決定該資源群組的參數值，讓您可以重複使用原則定義，以滿足相同的資源屬性，並具有不同的合規性需求。

您可以使用 JSON 來建立原則指派。 原則指派包含的元素：

- 顯示名稱
- description
- 中繼資料
- 強制模式
- 排除的範圍
- 原則定義
- 參數

例如，下列 JSON 會以動態參數顯示_DoNotEnforce_模式中的原則指派：

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

所有 Azure 原則範例都位於[Azure 原則的範例](../samples/index.md)。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用**displayName**和**description**來識別原則指派，並提供與特定資源集搭配使用的內容。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

## <a name="enforcement-mode"></a>強制模式

**EnforcementMode**屬性可讓客戶在現有資源上測試原則結果，而不需要在[Azure 活動記錄](../../../azure-monitor/platform/platform-logs-overview.md)中起始原則效果或觸發專案。 此案例通常稱為「What If」，並與安全的部署做法一致。 **enforcementMode**與[停用](./effects.md#disabled)的效果不同，因為這會導致根本無法評估資源。

此屬性具有下列值：

|[模式] |JSON 值 |類型 |手動修復 |活動記錄專案 |描述 |
|-|-|-|-|-|-|
|啟用 |預設值 |字串 |是 |是 |在資源建立或更新期間，會強制執行原則效果。 |
|停用 |DoNotEnforce |字串 |是 |否 | 在資源建立或更新期間不會強制執行原則效果。 |

如果未在原則或計畫定義中指定**enforcementMode** ，則會使用_預設_值。 即使**enforcementMode**設為_DoNotEnforce_，也可以針對[deployIfNotExists](./effects.md#deployifnotexists)原則啟動[補救](../how-to/remediate-resources.md)工作。

## <a name="excluded-scopes"></a>排除的範圍

指派的**範圍**包含所有子資源容器和子資源。 如果子資源容器或子資源不應套用定義，則可以藉由設定**notScopes**從評估中排除每個。 此屬性是一種陣列，可讓一或多個資源容器或資源排除在評估之外。 建立初始指派之後，可以新增或更新**notScopes** 。

## <a name="policy-definition-id"></a>原則定義識別碼

此欄位必須是原則定義或計畫定義的完整路徑名稱。
`policyDefinitionId`是字串，而不是陣列。 如果經常同時指派多個原則，建議您改用[計畫](./definition-structure.md#initiatives)。

## <a name="parameters"></a>參數

原則指派的這個區段會提供[原則定義或計畫定義](./definition-structure.md#parameters)中定義之參數的值。
這種設計可讓您以不同的資源重複使用原則或計畫定義，但檢查不同的商業價值或結果。

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

在此範例中，先前在原則定義中定義的參數`prefix`是`suffix`和。 此特定原則指派會`prefix`將設定為`suffix` **DeptA**和 **-LC**。 相同的原則定義可重複用於不同部門的一組不同參數，減少原則定義的重複程度和複雜度，同時提供彈性。

## <a name="next-steps"></a>後續步驟

- 瞭解[原則定義結構](./definition-structure.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 請參閱使用[Azure 管理群組來組織資源](../../management-groups/overview.md)的管理群組。