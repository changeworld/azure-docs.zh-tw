---
title: 模式：原則定義中的參數
description: 此 Azure 原則模式提供如何在原則定義中使用參數的範例。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170176"
---
# <a name="azure-policy-pattern-parameters"></a>Azure 原則模式：參數

您可以使用[參數](../concepts/definition-structure.md#parameters)將原則定義設定為動態，以減少所需的原則定義數目。 參數會在原則指派期間進行定義。 參數具有一組預先定義的屬性，用於描述參數和其使用方式。

## <a name="sample-1-string-parameters"></a>範例 1：字串參數

此原則定義會使用兩個參數 (**tagName** 和 **tagValue**) 來設定原則指派要在資源上尋找的內容。 此格式可讓原則用於任何數目的標記名稱和標記值組合，但只會維護單一原則定義。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>範例 1：說明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

在原則定義的這個部分中，**tagName** 參數會定義為「字串」  ，並提供描述供其使用。

然後，**policyRule.if** 區塊中會使用該參數讓原則變成動態的。 在這裡，參數會用來定義評估的欄位，也就是具有 **tagName** 值的標記。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>範例 2：陣列參數

此原則定義會使用單一參數 (**listOfBandwidthinMbps**) 來檢查 Express Route 線路資源是否已將頻寬設定設為其中一個已核准的值。 如果不相符，資源的建立或更新就會遭到[拒絕](../concepts/effects.md#deny)。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>範例 2：說明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

在原則定義的這個部分中，**listOfBandwidthinMbps** 參數會定義為「陣列」  ，並提供描述供其使用。 「陣列」  會有許多個要比對的值。

然後，在 **policyRule.if** 區塊中使用該參數。 因為是「陣列」  參數，您必須使用「陣列」  
[條件](../concepts/definition-structure.md#conditions)的 **in** 或 **notIn**。
在這裡，該參數會針對 **serviceProvider.bandwidthInMbps** 別名，作為其中一個已定義的值。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。