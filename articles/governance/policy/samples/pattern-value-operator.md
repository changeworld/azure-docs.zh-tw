---
title: 模式：原則定義中的值運算子
description: 此 Azure 原則模式提供如何在原則定義中使用值預算子的範例。
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072878"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure 原則模式：值運算子

[值](../concepts/definition-structure.md#value)運算子會針對指定[條件](../concepts/definition-structure.md#conditions)的提供值評估[參數](../concepts/definition-structure.md#parameters)、[支援的範本函式](../concepts/definition-structure.md#policy-functions)或常值。

> [!WARNING]
> 如果「範本函式」  的結果為錯誤，則原則評估會失敗。 失敗的評估隱含著**拒絕**的意思。 如需詳細資訊，請參閱[避免範本錯誤](../concepts/definition-structure.md#avoiding-template-failures)。

## <a name="sample-policy-definition"></a>原則定義範例

此原則定義會在資源上新增或取代參數 **tagName** (字串  ) 中指定的標記，並從資源所在的資源群組繼承 **tagName** 的值。 此評估會在建立或更新資源時進行。 作為 [modify](../concepts/effects.md#modify) 效果，您可以透過[補救工作](../how-to/remediate-resources.md)在現有資源上執行補救。

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>說明

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**值**運算子會用於**屬性**中的 **policyRule.if** 區塊。 在此範例中，**allOf** [邏輯運算子](../concepts/definition-structure.md#logical-operators)用來指出這兩個條件陳述式都必須為 true，才會發生 **modify** 效果。

**值**會針對空值的 **notEquals** 條件，評估 [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) 範本函式的結果。 如果父資源群組上有 **tagName** 中提供的標記名稱，則條件會評估為 true。

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。