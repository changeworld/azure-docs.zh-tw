---
title: 模式：使用方案來群組原則定義
description: 此 Azure 原則模式提供如何將原則定義群組到方案中的範例。
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: fc79775b79c9846ee435917731428627fc65549e
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649964"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure 原則模式：群組原則定義

方案是一組原則定義。 您可以藉由將相關的原則定義群組到單一物件中，建立有多個指派的單一指派。

## <a name="sample-initiative-definition"></a>範例方案定義

此方案會部署兩個原則定義，每一個都會採用 **tagName** 和 **tagValue** 參數。 此方案本身有兩個參數：**costCenterValue** 和 **productNameValue**。
這些方案參數會分別提供給每個已分組的原則定義。 此設計可讓現有原則定義盡可能地重複使用，同時限制建立的指派數目，並在有需要時再加以執行。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>說明

#### <a name="initiative-parameters"></a>計畫參數

方案可以定義其自己的參數，然後傳遞至已分組的原則定義。
在此範例中，**costCenterValue** 和 **productNameValue** 都會定義為方案參數。 這些值會在指派方案時提供。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>包括原則定義

如果原則定義可接受參數，則每個包含的原則定義都必須提供 **policyDefinitionId** 和**參數**陣列。 在下列程式碼片段中，包含的原則定義會採用兩個參數：**tagName** 和 **tagValue**。 **tagName** 會使用常值來定義，但 **tagValue** 會使用方案所定義的 **costCenterValue** 參數。 此值的傳遞可改善重複使用的狀況。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。