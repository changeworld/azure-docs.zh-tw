---
title: 模式：原則定義的效果
description: 此 Azure 原則模式提供如何使用不同原則定義效果的範例。
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 9c7e7689aa837a2dba0992112df3cd36ac9affe6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565739"
---
# <a name="azure-policy-pattern-effects"></a>Azure 原則模式：效果

Azure 原則有許多[效果](../concepts/effects.md)，可決定服務如何回應不符合規範的資源。 有些效果很簡單，不需要原則定義中的其他屬性，而有些則需要數個屬性。

## <a name="sample-1-simple-effect"></a>範例 1：簡單效果

此原則定義會檢查 **tagName** 參數中定義的標記是否存在於已評估的資源上。 如果標記尚不存在，則會觸發 [modify](../concepts/effects.md#modify) 效果，使用 **tagValue** 參數中的值新增標記。

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>範例 1：說明

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

**modify** 效果需要 **policyRule.then.details** 區塊，以定義 **roleDefinitionIds** 和 **operations**。 這些參數會告知 Azure 原則需要哪些角色才能新增標記並補救資源，以及要執行哪個 **modify** 作業。 在此範例中，**作業**是「新增」  ，而參數則是用來設定標記和其值。

## <a name="sample-2-complex-effect"></a>範例 2：複雜效果

此原則定義會在延伸模組不存在時稽核每個虛擬機器，延伸模組會在 **publisher** 和 **type** 參數中定義。 其使用 [auditIfNotExists](../concepts/effects.md#auditifnotexists) 來檢查與虛擬機器相關的資源，以查看是否有執行個體符合定義的參數。 此範例會檢查**延伸模組**類型。

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>範例 2：說明

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

**auditIfNotExists** 效果需要 **policyRule.then.details** 區塊，以定義要尋找的 **type** 和 **existenceCondition**。 **existenceCondition** 會使用原則語言元素 (例如[邏輯運算子](../concepts/definition-structure.md#logical-operators)) 來判斷相符的相關資源是否存在。 在此範例中，針對每個[別名](../concepts/definition-structure.md#aliases)檢查的值會定義在參數中。

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。