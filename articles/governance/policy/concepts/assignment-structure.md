---
title: 原則指派結構的詳細資料
description: 描述 Azure 原則用來將原則定義和參數關聯至資源以進行評估的原則指派定義。
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 969274d72724c8d0a8f10f86f614fe2c50d066f7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520708"
---
# <a name="azure-policy-assignment-structure"></a>Azure 原則指派結構

Azure 原則使用原則指派來定義哪些資源會指派給哪些原則或方案。 原則指派可以在指派時判斷該資源群組的參數值，讓您可以重複使用原則定義，以因應合規性的不同需求來處理相同的資源屬性。

您可以使用 JSON 來建立原則指派。 原則指派包含下列專案的元素：

- 顯示名稱
- description
- 中繼資料
- 強制模式
- 排除的範圍
- 原則定義
- 參數

例如，下列 JSON 以 _DoNotEnforce_ 模式顯示具有動態參數的原則指派：

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

所有 Azure 原則範例都位於 [Azure 原則範例](../samples/index.md)中。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別原則指派，並提供用於特定資源集的內容。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

## <a name="enforcement-mode"></a>強制模式

**EnforcementMode**屬性可讓客戶在不啟動原則效果或觸發[Azure 活動記錄](../../../azure-monitor/platform/platform-logs-overview.md)中的專案的情況下，測試現有資源上原則的結果。 此案例通常稱為「What If」，並符合安全的部署做法。 **enforcementMode** 與 [停用](./effects.md#disabled) 的效果不同，因為這樣會造成根本無法進行資源評估。

這個屬性具有下列值：

|[模式] |JSON 值 |類型 |手動補救 |活動記錄專案 |描述 |
|-|-|-|-|-|-|
|啟用 |預設 |字串 |是 |是 |原則效果會在建立或更新資源期間強制執行。 |
|已停用 |DoNotEnforce |字串 |是 |否 | 在建立或更新資源期間，不會強制執行原則效果。 |

如果未在原則或計畫定義中指定 **enforcementMode** ，則會使用 _預設_ 值。 即使將**enforcementMode**設為_DoNotEnforce_，也可以針對[deployIfNotExists](./effects.md#deployifnotexists)原則啟動[補救](../how-to/remediate-resources.md)工作。

## <a name="excluded-scopes"></a>排除的範圍

指派的 **範圍** 包括所有子資源容器和子資源。 如果子資源容器或子資源不應套用定義，則可以藉由設定 **notScopes**將每個資源排除在評估之外。 這個屬性是一種陣列，可讓您從評估中排除一或多個資源容器或資源。 建立初始指派之後，可以新增或更新**notScopes** 。

## <a name="policy-definition-id"></a>原則定義識別碼

此欄位必須是原則定義或計畫定義的完整路徑名稱。
`policyDefinitionId` 是字串，而不是陣列。 如果通常會同時指派多個原則，建議您改為使用 [方案](./initiative-definition-structure.md) 。

## <a name="parameters"></a>參數

原則指派的這個區段會提供 [原則定義或計畫定義](./definition-structure.md#parameters)中定義之參數的值。 此設計可讓您以不同的資源重複使用原則或計畫定義，但請檢查不同的商業價值或結果。

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

在此範例中，原則定義中先前定義的參數為 `prefix` 和 `suffix` 。 此特定原則指派會設定 `prefix` 為 **DeptA** 和 `suffix` **-LC**。 相同的原則定義可重複使用不同部門的不同參數集合，以減少原則定義的重複和複雜性，同時提供彈性。

## <a name="next-steps"></a>後續步驟

- 了解[原則定義結構](./definition-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。