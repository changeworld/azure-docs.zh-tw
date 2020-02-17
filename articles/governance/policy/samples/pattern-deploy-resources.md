---
title: 模式：使用原則定義來部署資源
description: 此 Azure 原則模式提供如何使用原則定義來部署資源的範例。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169986"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure 原則模式：部署資源

[deployIfNotExists](../concepts/effects.md#deployifnotexists) 效果讓您可以在建立或更新不符合規範的資源時，部署 [Azure Resource Manager 範本](../../../azure-resource-manager/templates/overview.md)。 這種方法會比使用 [deny](../concepts/effects.md#deny) 效果更好，因為其可讓資源繼續建立，但又能確保可進行變更以使資源符合規範。

## <a name="sample-policy-definition"></a>原則定義範例

此原則定義會使用 **field** 運算子來評估所建立或更新的資源 `type`。 當該資源是「Microsoft.Network/virtualNetworks」  時，原則會在新資源或已更新資源的位置中尋找網路監看員。 如果找不到相符的網路監看員，則會部署 Resource Manager 範本來建立遺失的資源。

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>說明

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

**properties.policyRule.then.details** 區塊會告訴 Azure 原則要在 **properties.policyRule.if** 區塊中尋找什麼與所建立或更新的資源相關的項目。 在此範例中，資源群組中 **networkWatcherRG** 中的網路監看員必須存在，且**欄位** `location` 要等於新資源或已更新資源的位置。 使用 `field()` 函式可讓 **existenceCondition** 存取新資源或已更新資源上的屬性，尤其是 `location` 屬性。

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

**properties.policyRule.then.details** 區塊中的 **roleDefinitionIds**「陣列」  屬性會告訴原則定義，受控識別需要哪些權限才能部署所包含的 Resource Manager 範本。 此屬性必須設定為將具有範本部署所需權限的角色包含在內，但應使用「最低權限原則」的概念，而且只具有所需的作業，而沒有其他任何項目。

#### <a name="deployment-template"></a>部署範本

原則定義的**部署**部分具有定義了三個核心元件的**屬性**區塊：

- **模式** - 此屬性會設定範本的[部署模式](../../../azure-resource-manager/templates/deployment-modes.md)。

- **範本** - 此屬性包含範本本身。 在此範例中，**位置**範本參數會設定新網路監看員資源的位置。

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **參數** - 此屬性會定義提供給**範本**的參數。 參數名稱必須符合**範本**中所定義的名稱。 在此範例中，參數會命名為**位置**以便相符。 **位置**的值會再次使用 `field()` 函式來取得已評估資源的值，也就是 **policyRule.if** 區塊中的虛擬網路。

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>後續步驟

- 檢閱其他[模式和內建定義](./index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。