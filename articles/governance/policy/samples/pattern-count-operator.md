---
title: 模式：原則定義中的 Count 運算子
description: 此 Azure 原則模式提供如何在原則定義中使用 Count 預算子的範例。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170376"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure 原則模式：Count 運算子

[Count](../concepts/definition-structure.md#count) 運算子會評估 \[\*\] 別名的成員。

## <a name="sample-policy-definition"></a>原則定義範例

此原則定義會針對已設定為允許輸入遠端桌面通訊協定 (RDP) 流量的網路安全性群組進行[稽核](../concepts/effects.md#audit)。

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>說明

 運算子的核心元件是 field  、where  和條件。 下列程式碼片段中已醒目提示每個元件。

- field  會告訴 Count 應評估哪一個[別名](../concepts/definition-structure.md#aliases)的成員。 在這裡，我們將查看網路安全性群組的 **securityRules\[\*\]** 別名「陣列」  。
- where  會使用原則語言定義哪些「陣列」  成員符合準則。 在此範例中，**allOf** 邏輯運算子會將別名「陣列」  屬性的三個不同條件評估分組：_direction_、_access_ 和 _destinationPortRange_。
- 此範例中的 Count 條件為**大於**。 當別名「陣列」  的一個或多個成員符合 where  子句時，Count 會評估為 true。

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。