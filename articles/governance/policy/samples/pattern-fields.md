---
title: 模式：原則定義中的欄位屬性
description: 此 Azure 原則模式提供如何在原則定義中使用欄位屬性的範例。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170256"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure 原則模式：欄位屬性

[欄位](../concepts/definition-structure.md#fields)運算子會針對在指定[條件](../concepts/definition-structure.md#conditions)下提供的值，評估指定的屬性或[別名](../concepts/definition-structure.md#aliases)。

## <a name="sample-policy-definition"></a>原則定義範例

此原則定義可讓您定義符合組織地理位置需求的允許區域。 允許的資源會在 **listOfAllowedLocations** (陣列  ) 中定義。 符合定義的資源會遭到[拒絕](../concepts/effects.md#deny)。

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>說明

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

**欄位**運算子會在[邏輯運算子](../concepts/definition-structure.md#logical-operators) **allOf** 中使用三次。

- 第一次使用時會透過 **listOfAllowedLocations** 參數的 **notIn** 條件來評估 `location` 屬性。 **notIn** 會在預期有「陣列」  的情況下運作，而該參數就是「陣列」  。 如果已建立或已更新資源的 `location` 不在核准的清單中，則此元素會評估為 true。
- 第二次使用時也會評估 `location` 屬性，但會使用 **notEquals** 條件來查看資源是否為「全域」  。 如果已建立或已更新資源的 `location` 不是「全域」  ，則此元素會評估為 true。
- 最後一次使用時會評估 `type` 屬性，並使用 **notEquals** 條件來驗證資源類型不是 _Microsoft.AzureActiveDirectory/b2cDirectories_。 如果不是，此元素會評估為 true。

如果 **allOf** 邏輯運算子中的三個條件陳述式都評估為 true，則 Azure 原則會封鎖資源建立或更新。

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。