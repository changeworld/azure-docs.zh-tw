---
title: 策略分配結構的詳細資訊
description: 描述 Azure 策略用於將策略定義和參數與資源相關聯以便評估的策略分配定義。
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265294"
---
# <a name="azure-policy-assignment-structure"></a>Azure 原則指派結構

Azure 策略使用策略分配來定義分配哪些策略或計畫的資源。 策略分配可以在分配時確定該資源組的參數值，從而可以重用具有不同合規性需求的相同資源屬性的策略定義。

您可以使用 JSON 創建策略分配。 策略分配包含以下元素：

- 顯示名稱
- description
- 中繼資料
- 強制模式
- 策略定義
- 參數

例如，以下 JSON 顯示了具有動態參數的_DoNotEnforce_模式下的策略分配：

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
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

所有 Azure 策略示例都在[Azure 策略示例](../samples/index.md)中。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用**顯示名稱**和**說明**來標識策略分配，並提供上下文供其與特定資源集一起使用。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

## <a name="enforcement-mode"></a>執行模式

**強制Mode**屬性使客戶能夠在不啟動策略效果或觸發[Azure 活動日誌](../../../azure-monitor/platform/platform-logs-overview.md)中的條目的情況下測試對現有資源的策略的結果。 此方案通常稱為"假設"，並符合安全部署實踐。 **強制模式**與[禁用](./effects.md#disabled)效果不同，因為該效果完全防止資源評估發生。

此屬性具有以下值：

|[模式] |JSON 值 |類型 |手動修復 |活動日誌條目 |描述 |
|-|-|-|-|-|-|
|啟用 |預設 |字串 |是 |是 |策略效果在資源創建或更新期間強制執行。 |
|已停用 |不強制 |字串 |是 |否 | 策略效果在資源創建或更新期間未強制執行。 |

如果在策略或計畫定義中未指定**強制模式**，則使用值 _"預設_"。 可以啟動部署["不存在](./effects.md#deployifnotexists)"策略的[修正任務](../how-to/remediate-resources.md)，即使**強制模式**設置為 _"不強制_"。

## <a name="policy-definition-id"></a>策略定義 ID

此欄位必須是策略定義或計畫定義的完整路徑名稱。
`policyDefinitionId`是字串，而不是陣列。 建議，如果多個策略通常一起分配，則使用[一項計畫](./definition-structure.md#initiatives)。

## <a name="parameters"></a>參數

策略分配的此部分為[策略定義或計畫定義](./definition-structure.md#parameters)中定義的參數提供了值。
通過此設計，可以使用不同的資源重用策略或計畫定義，但檢查不同的業務價值或結果。

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

在此示例中，策略定義中以前定義的參數是`prefix`和`suffix`。 此特定策略分配集`prefix`到**部門**A`suffix`和 **-LC**。 同一策略定義可重用為不同部門設置不同的參數集，從而減少策略定義的重複和複雜性，同時提供靈活性。

## <a name="next-steps"></a>後續步驟

- 瞭解[策略定義結構](./definition-structure.md)。
- 瞭解如何[以程式設計方式創建策略](../how-to/programmatically-create.md)。
- 瞭解如何[獲取合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。