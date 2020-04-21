---
title: 模式：原則定義中的邏輯運算子
description: 此 Azure 原則模式提供如何在原則定義中使用邏輯運算子的範例。
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 691383b1f8ae34bbd51ce7f4f9310980e3c66537
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272503"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure 原則模式：邏輯運算子

原則定義可以包含數個條件陳述式。 您可能需要每個陳述式都成立，或只需要其中一些條件成立。 為了支援這些需求，語言會有 **not**、**allOf** 及 **anyOf** 的[邏輯運算子](../concepts/definition-structure.md#logical-operators)。 這些項目都是選擇性的，而且可以透過巢狀方式建立複雜的案例。

## <a name="sample-1-one-logical-operator"></a>範例 1：一個邏輯運算子

此原則定義會評估 CosmosDB 帳戶，以查看是否已設定自動容錯移轉和多個寫入位置。 如果未設定，[audit](../concepts/effects.md#audit) 會在建立或更新不符合規範的資源時觸發，並建立記錄項目。

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>範例 1：說明

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**policyRule.if** 區塊會使用單一 **allOf** 來確保所有三個條件都成立。
只有當所有這些條件都評估為 true 時，**audit** 效果才會觸發。

## <a name="sample-2-multiple-logical-operators"></a>範例 2：多個邏輯運算子

此原則定義會評估用於命名模式的資源。 如果資源不相符，就會遭到[拒絕](../concepts/effects.md#deny)。

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>範例 2：說明

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

此 **policyRule.if** 區塊也包含單一 **allOf**，但每個條件都會以 **not** 邏輯運算子來包裝。 **not** 邏輯運算子內的條件會先進行評估，然後再評估 **not** 來判斷整個子句為 true 或 false。 如果 **not** 邏輯運算子評估為 true，則會觸發原則效果。

## <a name="sample-3-combining-logical-operators"></a>範例 3：結合邏輯運算子

此原則定義會評估 Java Spring 帳戶，以查看是否未啟用任何追蹤，或追蹤是否處於成功狀態。

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>範例 3：說明

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

此 **policyRule.if** 區塊同時包含 **allOf** 和 **anyOf** 邏輯運算子。 只要其中一個包含的條件為 True，**anyOf** 邏輯運算子就會評估為 True。 當「類型」  是 **allOf** 的核心時，則一律會評估為 True。 如果「類型」  和 **anyOf** 中的其中一個條件為 True，則會觸發原則效果。

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。