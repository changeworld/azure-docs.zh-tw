---
title: 模式：使用原則定義中的標記
description: 此 Azure 原則模式提供如何在原則定義中新增參數化標記或從資源群組繼承標記的範例。
ms.date: 05/20/2020
ms.topic: sample
ms.openlocfilehash: b71a21fadfc8dec3da2feabbce127303174a95d3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704341"
---
# <a name="azure-policy-pattern-tags"></a>Azure 原則模式：標記

[標記](../../..//azure-resource-manager/management/tag-resources.md)是管理、組織及治理 Azure 資源的重要部分。 Azure 原則可讓您使用[修改](../concepts/effects.md#modify)效果和[補救工作](../how-to/remediate-resources.md)，大規模地在新資源和現有資源上設定標記。

## <a name="sample-1-parameterize-tags"></a>範例 1：參數化標記

此原則定義會使用兩個參數 (**tagName** 和 **tagValue**) 來設定原則指派要在資源群組上尋找的內容。 此格式可讓原則定義用於任何數目的標記名稱和標記值組合，但只會維護單一原則定義。

> [!NOTE]
> 雖然此原則定義模式類似於[模式：參數 - 範例 #1](./pattern-parameters.md#sample-1-string-parameters) 中的模式，但此範例會使用 [所有] **模式**和目標資源群組。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>範例 1：說明

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

在此範例中，**模式**會設定為 [所有]，因為是以資源群組為目標。 在大部分情況下，使用標記時，**模式**應該設定為 [已編製索引]。 如需詳細資訊，請參閱[模式](../concepts/definition-structure.md#resource-manager-modes)。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

在原則定義的這個部分中，`concat` 會將參數化的 **tagName** 參數和 `tags['name']` 格式結合，以指示**欄位**評估 **tagValue** 參數的標記。
使用 **notEquals** 時，如果 **tags\[tagName\]** 不等於 **tagValue**，則會觸發**修改**效果。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

在這裡，**addOrReplace** 作業會使用相同格式來使用參數化標記值的，以在評估的資源群組上建立或更新所需的標記值。

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>範例 2：從資源群組繼承標記值

此原則定義會使用 **tagName** 參數來判斷要從父資源群組繼承哪一個標記的值。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>範例 2：說明

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

在此範例中，**模式**會設定為 [已編製索引]，因為其不是以資源群組或訂用帳戶為目標，即使是從資源群組取得值也是一樣。 如需詳細資訊，請參閱[模式](../concepts/definition-structure.md#resource-manager-modes)。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**policyRule.if**會使用像 [Sample #1](#sample-1-parameterize-tags) 這類的 `concat` 來評估 **tagName** 的值，但會使用 `resourceGroup()` 函式將其與父資源群組上的相同標記值進行比較。 這裡的第二個子句會檢查資源群組上的標記是否具有值，且不是 Null。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

在這裡，指派給資源上 **tagName** 標記的值也會使用 `resourceGroup()` 函式從父資源群組取得值。 如此一來，您就可以從父資源群組繼承標記。 如果您已經建立資源，但未新增標記，則此相同的原則定義和 [補救工作](../how-to/remediate-resources.md)可以更新現有資源。

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。